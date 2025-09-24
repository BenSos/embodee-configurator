<template>
  <div class="customization-panel">
    <div class="card">
      <h2 class="panel-title">Customize Your Product</h2>
      
      <!-- Body Color Selection -->
      <div class="customization-section" v-if="bodyColors.length > 0">
        <div class="section-header">
          <input 
            type="radio" 
            id="body-section" 
            name="active-section" 
            value="body"
            v-model="activeSection"
            class="section-radio"
          />
          <label for="body-section" class="section-label">Body</label>
        </div>
        
        <div v-if="activeSection === 'body'" class="section-content">
          <div class="color-grid">
            <div 
              v-for="color in bodyColors" 
              :key="color.id"
              class="color-swatch"
              :class="{ selected: selectedBodyColor === color.id }"
              @click="selectBodyColor(color.id)"
            >
              <div 
                class="color-preview" 
                :style="{ backgroundColor: '#' + color.value }"
              ></div>
              <span class="color-name">{{ color.name }}</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Swoosh Options -->
      <div class="customization-section" v-if="swooshOptions.length > 0">
        <div class="section-header">
          <input 
            type="radio" 
            id="swoosh-section" 
            name="active-section" 
            value="swoosh"
            v-model="activeSection"
            class="section-radio"
          />
          <label for="swoosh-section" class="section-label">
            Swoosh Option (determines available Graphic Styles)
          </label>
        </div>
        
        <div v-if="activeSection === 'swoosh'" class="section-content">
          <div class="radio-group">
            <div 
              v-for="option in swooshOptions" 
              :key="option.id"
              class="radio-item"
              :class="{ selected: selectedSwooshOption === option.id }"
              @click="selectSwooshOption(option.id)"
            >
              <input 
                type="radio" 
                :id="`swoosh-${option.id}`"
                name="swoosh-option" 
                :value="option.id"
                v-model="selectedSwooshOption"
              />
              <label :for="`swoosh-${option.id}`">{{ option.name }}</label>
            </div>
          </div>
        </div>
      </div>

      <!-- Graphic Styles -->
      <div class="customization-section" v-if="graphicStyles.length > 0">
        <div class="section-header">
          <input 
            type="radio" 
            id="graphics-section" 
            name="active-section" 
            value="graphics"
            v-model="activeSection"
            class="section-radio"
          />
          <label for="graphics-section" class="section-label">Graphic Styles</label>
        </div>
        
        <div v-if="activeSection === 'graphics'" class="section-content">
          <div class="radio-group">
            <div 
              v-for="style in graphicStyles" 
              :key="style.id"
              class="radio-item"
              :class="{ selected: selectedGraphicStyle === style.id }"
              @click="selectGraphicStyle(style.id)"
            >
              <input 
                type="radio" 
                :id="`graphic-${style.id}`"
                name="graphic-style" 
                :value="style.id"
                v-model="selectedGraphicStyle"
              />
              <label :for="`graphic-${style.id}`">{{ style.name }}</label>
            </div>
          </div>
        </div>
      </div>

      <!-- Text Customization -->
      <div class="customization-section" v-if="textElements.length > 0">
        <div class="section-header">
          <input 
            type="radio" 
            id="text-section" 
            name="active-section" 
            value="text"
            v-model="activeSection"
            class="section-radio"
          />
          <label for="text-section" class="section-label">Text Customization</label>
        </div>
        
        <div v-if="activeSection === 'text'" class="section-content">
          <div v-for="textElement in textElements" :key="textElement.id" class="text-customization">
            <h4>{{ textElement.name }}</h4>
            
            <!-- Text Input -->
            <div class="form-group" v-if="textElement.props.text">
              <label class="form-label">Text</label>
              <input 
                type="text" 
                class="form-control"
                :value="textElement.props.text.value"
                @input="updateTextProperty(textElement.id, 'text', $event.target.value)"
                :maxlength="textElement.props.text.maxlength?.value || 50"
              />
            </div>

            <!-- Font Selection -->
            <div class="form-group" v-if="textElement.props.font">
              <label class="form-label">Font</label>
              <select 
                class="form-control"
                :value="textElement.props.font.value"
                @change="updateTextProperty(textElement.id, 'font', $event.target.value)"
              >
                <option 
                  v-for="font in textElement.props.font.items()" 
                  :key="font.id" 
                  :value="font.id"
                >
                  {{ font.name }}
                </option>
              </select>
            </div>

            <!-- Fill Color -->
            <div class="form-group" v-if="textElement.props.fillcolor">
              <label class="form-label">Fill Color</label>
              <div class="color-grid">
                <div 
                  v-for="color in textElement.props.fillcolor.items()" 
                  :key="color.id"
                  class="color-swatch"
                  :class="{ selected: textElement.props.fillcolor.selected() === color.id }"
                  @click="updateTextProperty(textElement.id, 'fillcolor', color.id)"
                >
                  <div 
                    class="color-preview" 
                    :style="{ backgroundColor: '#' + color.value }"
                  ></div>
                  <span class="color-name">{{ color.name }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CustomizationPanel',
  props: {
    uiStructure: {
      type: Object,
      default: null
    },
    library: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      activeSection: 'body',
      selectedBodyColor: null,
      selectedSwooshOption: null,
      selectedGraphicStyle: null,
      bodyColors: [],
      swooshOptions: [],
      graphicStyles: [],
      textElements: []
    }
  },
  watch: {
    uiStructure: {
      handler(newStructure) {
        if (newStructure) {
          this.parseUIStructure(newStructure)
        }
      },
      immediate: true
    }
  },
  methods: {
    parseUIStructure(structure) {
      this.bodyColors = []
      this.swooshOptions = []
      this.graphicStyles = []
      this.textElements = []

      this.traverseNodes(structure)
    },

    traverseNodes(nodes) {
      if (!nodes || !Array.isArray(nodes)) return

      nodes.forEach(node => {
        // Body colors (components with color properties)
        if (node.type === 'component' && node.props?.color) {
          const colors = node.props.color.items()
          if (colors && Object.keys(colors).length > 0) {
            this.bodyColors = Object.values(colors)
            if (!this.selectedBodyColor && this.bodyColors.length > 0) {
              this.selectedBodyColor = node.props.color.selected()
            }
          }
        }

        // Swoosh options
        if (node.type === 'component' && node.name?.toLowerCase().includes('swoosh')) {
          if (node.props?.material) {
            const materials = node.props.material.items()
            if (materials && Object.keys(materials).length > 0) {
              this.swooshOptions = Object.values(materials)
              if (!this.selectedSwooshOption && this.swooshOptions.length > 0) {
                this.selectedSwooshOption = node.props.material.selected()
              }
            }
          }
        }

        // Graphic styles
        if (node.type === 'print' || (node.type === 'decal' && node.subtype === 'graphic')) {
          if (node.props?.material) {
            const materials = node.props.material.items()
            if (materials && Object.keys(materials).length > 0) {
              this.graphicStyles = Object.values(materials)
              if (!this.selectedGraphicStyle && this.graphicStyles.length > 0) {
                this.selectedGraphicStyle = node.props.material.selected()
              }
            }
          }
        }

        // Text elements
        if (node.type === 'decal' && node.subtype === 'text') {
          this.textElements.push({
            id: node.name,
            name: node.name,
            props: node.props
          })
        }

        // Traverse child nodes
        if (node.available && Array.isArray(node.available)) {
          this.traverseNodes(node.available)
        }
      })
    },

    selectBodyColor(colorId) {
      this.selectedBodyColor = colorId
      this.$emit('customization-change', {
        type: 'body-color',
        value: colorId
      })
    },

    selectSwooshOption(optionId) {
      this.selectedSwooshOption = optionId
      this.$emit('customization-change', {
        type: 'swoosh-option',
        value: optionId
      })
    },

    selectGraphicStyle(styleId) {
      this.selectedGraphicStyle = styleId
      this.$emit('customization-change', {
        type: 'graphic-style',
        value: styleId
      })
    },

    updateTextProperty(elementId, property, value) {
      this.$emit('text-property-change', {
        elementId,
        property,
        value
      })
    }
  }
}
</script>

<style scoped>
.customization-panel {
  height: fit-content;
}

.panel-title {
  font-size: 20px;
  font-weight: 600;
  margin-bottom: 20px;
  color: #111827;
}

.customization-section {
  margin-bottom: 24px;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  overflow: hidden;
}

.section-header {
  background: #f9fafb;
  padding: 12px 16px;
  border-bottom: 1px solid #e5e7eb;
}

.section-radio {
  display: none;
}

.section-label {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 500;
  color: #374151;
  cursor: pointer;
  margin: 0;
}

.section-label::before {
  content: '';
  width: 16px;
  height: 16px;
  border: 2px solid #d1d5db;
  border-radius: 50%;
  display: inline-block;
  position: relative;
}

.section-radio:checked + .section-label::before {
  border-color: #3b82f6;
  background-color: #3b82f6;
}

.section-radio:checked + .section-label::after {
  content: '';
  position: absolute;
  left: 4px;
  top: 4px;
  width: 8px;
  height: 8px;
  background: white;
  border-radius: 50%;
}

.section-content {
  padding: 16px;
}

.text-customization {
  margin-bottom: 20px;
  padding-bottom: 20px;
  border-bottom: 1px solid #e5e7eb;
}

.text-customization:last-child {
  border-bottom: none;
  margin-bottom: 0;
  padding-bottom: 0;
}

.text-customization h4 {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 12px;
  color: #374151;
}
</style>
