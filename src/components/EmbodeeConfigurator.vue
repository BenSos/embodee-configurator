<template>
  <div class="embodee-configurator">
    <div v-if="loading" class="loading">
      <div class="spinner"></div>
      Loading 3D Configurator...
    </div>
    <div v-else-if="error" class="error">
      <h3>Error Loading Configurator</h3>
      <p>{{ error }}</p>
      <button @click="retry" class="btn btn-primary">Retry</button>
    </div>
    <div v-else class="configurator-container">
      <!-- The Embodee 3D viewer will be mounted here -->
      <div ref="configuratorContainer" id="embodee-configurator"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'EmbodeeConfigurator',
  props: {
    workspaceId: {
      type: String,
      required: true
    },
    productCode: {
      type: String,
      required: true
    },
    designId: {
      type: String,
      default: null
    },
    variant: {
      type: String,
      default: null
    },
    width: {
      type: Number,
      default: 640
    },
    height: {
      type: Number,
      default: 640
    },
    quality: {
      type: String,
      default: 'high',
      validator: (value) => ['high', 'medium', 'low', 'original'].includes(value)
    },
    bgColor: {
      type: String,
      default: null
    }
  },
  data() {
    return {
      loading: true,
      error: null,
      configurator: null
    }
  },
  mounted() {
    // Wait for the DOM to be fully rendered
    this.$nextTick(() => {
      // Additional delay to ensure the element is available
      setTimeout(() => {
        this.initializeConfigurator()
      }, 100)
    })
  },
  methods: {
    async initializeConfigurator() {
      try {
        this.loading = true
        this.error = null

        // Check if the container element exists
        const container = document.getElementById('embodee-configurator')
        if (!container) {
          throw new Error('Container element not found. Please try again.')
        }

        console.log('Container element found:', container)

        // Load the Embodee loader script
        await this.loadEmbodeeScript()

        // Set global variables (matching sandbox implementation)
        window.WORKSPACE_ID = this.workspaceId
        window.PRODUCT_CODE = this.productCode

        // Initialize the configurator
        const options = {
          workspaceID: this.workspaceId,
          productCode: this.productCode,
          containerID: 'embodee-configurator',
          width: this.width,
          height: this.height,
          quality: this.quality
        }

        if (this.designId) {
          options.designID = this.designId
        }

        if (this.variant) {
          options.variant = this.variant
        }

        if (this.bgColor) {
          options.bgColor = this.bgColor
        }

        // Initialize the configurator
        this.configurator = await window.EmbodeeLoader.init(options)

        // Set up event listeners
        this.setupEventListeners()

        this.loading = false
        this.$emit('configurator-ready', this.configurator)

      } catch (error) {
        console.error('Failed to initialize Embodee Configurator:', error)
        this.error = error.message || 'Failed to load configurator'
        this.loading = false
        this.$emit('configurator-error', error)
      }
    },

    async loadEmbodeeScript() {
      return new Promise((resolve, reject) => {
        if (window.EmbodeeLoader) {
          resolve()
          return
        }

        // Load CSS first
        this.loadEmbodeeCSS()
        
        // Load the main script
        const script = document.createElement('script')
        script.src = 'http://embodee.app/scripts/configurator/js/true/sandbox/_/home?v=1.32.17108'
        script.type = 'text/javascript'
        script.nonce = ''
        script.onload = resolve
        script.onerror = () => reject(new Error('Failed to load Embodee script'))
        document.head.appendChild(script)
      })
    },

    loadEmbodeeCSS() {
      // Load sandbox CSS
      const sandboxCSS = document.createElement('link')
      sandboxCSS.rel = 'stylesheet'
      sandboxCSS.type = 'text/css'
      sandboxCSS.media = 'all'
      sandboxCSS.href = 'http://embodee.app/scripts/configurator/css/true/sandbox/_/home?v=1.32.17108'
      document.head.appendChild(sandboxCSS)

      // Load loader CSS
      const loaderCSS = document.createElement('link')
      loaderCSS.rel = 'stylesheet'
      loaderCSS.type = 'text/css'
      loaderCSS.media = 'all'
      loaderCSS.href = 'http://embodee.app/scripts/configurator/css/true/loader/_/home?v=1.32.17108'
      document.head.appendChild(loaderCSS)
    },

    setupEventListeners() {
      if (!this.configurator) return

      const events = this.configurator.eventIDs

      // Product ready event
      this.configurator.subscribe(events.productReady, () => {
        this.$emit('product-ready', {
          uiStructure: this.configurator.uiStructure,
          library: this.configurator.library,
          config: this.configurator.config
        })
      })

      // Loading events
      this.configurator.subscribe(events.loadStart, () => {
        this.$emit('load-start')
      })

      this.configurator.subscribe(events.loadProgress, (percent) => {
        this.$emit('load-progress', percent)
      })

      this.configurator.subscribe(events.loadEnd, () => {
        this.$emit('load-end')
      })

      // Error events
      this.configurator.subscribe(events.error, (errorData) => {
        this.$emit('configurator-error', errorData)
      })

      this.configurator.subscribe(events.textureError, (errorData) => {
        this.$emit('texture-error', errorData)
      })

      // User interaction events
      this.configurator.subscribe(events.meshSelected, (data) => {
        this.$emit('mesh-selected', data)
      })

      this.configurator.subscribe(events.mouseDragged, () => {
        this.$emit('mouse-dragged')
      })

      // Component value changes
      this.configurator.subscribe(events.componentValueChanged, (data) => {
        this.$emit('component-value-changed', data)
      })
    },

    retry() {
      this.initializeConfigurator()
    }
  }
}
</script>

<style scoped>
.embodee-configurator {
  width: 100%;
  height: 100%;
  min-height: 500px;
}

.configurator-container {
  width: 100%;
  height: 100%;
  min-height: 500px;
  position: relative;
}

.configurator-container iframe {
  width: 100%;
  height: 600px;
  border: none;
  border-radius: 8px;
  background: white;
  display: block;
  overflow: hidden;
}

.loading {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 40px;
  color: #6b7280;
  background: white;
  border-radius: 8px;
  min-height: 500px;
}

.error {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px;
  background: #fef2f2;
  border: 1px solid #fecaca;
  border-radius: 8px;
  color: #dc2626;
  min-height: 500px;
}

.error h3 {
  margin-bottom: 8px;
  font-size: 18px;
  font-weight: 600;
}

.error p {
  margin-bottom: 16px;
  text-align: center;
}

.spinner {
  width: 20px;
  height: 20px;
  border: 2px solid #e5e7eb;
  border-top: 2px solid #3b82f6;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-right: 8px;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>