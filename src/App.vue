<template>
  <div id="app">
    <header class="app-header">
      <div class="container">
        <h1 class="app-title">Embodee Configurator</h1>
        <p class="app-subtitle">Interactive 3D Product Customization</p>
      </div>
    </header>

    <main class="app-main">
      <div class="container">
        <div class="grid grid-3">
          <!-- Left Panel: Customization Options -->
          <aside class="customization-sidebar">
            <CustomizationPanel
              :ui-structure="uiStructure"
              :library="library"
              @customization-change="handleCustomizationChange"
              @text-property-change="handleTextPropertyChange"
            />
          </aside>

          <!-- Center: 3D Product Viewer -->
          <section class="product-viewer-section">
            <div class="product-viewer">
              <EmbodeeConfigurator
                ref="configurator"
                :workspace-id="workspaceId"
                :product-code="productCode"
                :design-id="designId"
                :variant="variant"
                :width="640"
                :height="640"
                :quality="quality"
                :bg-color="bgColor"
                @configurator-ready="handleConfiguratorReady"
                @product-ready="handleProductReady"
                @configurator-error="handleConfiguratorError"
                @load-start="handleLoadStart"
                @load-progress="handleLoadProgress"
                @load-end="handleLoadEnd"
                @mesh-selected="handleMeshSelected"
                @component-value-changed="handleComponentValueChanged"
              />
            </div>
            
            <!-- Loading Overlay -->
            <div v-if="loading" class="loading-overlay">
              <div class="loading-content">
                <div class="spinner"></div>
                <p>Loading 3D Model...</p>
                <div v-if="loadProgress > 0" class="progress-bar">
                  <div class="progress-fill" :style="{ width: loadProgress + '%' }"></div>
                </div>
              </div>
            </div>
          </section>

          <!-- Right Panel: Product Information -->
          <aside class="product-info-sidebar">
            <ProductInfoPanel
              :product-data="productData"
              :customization-summary="customizationSummary"
              @add-to-cart="handleAddToCart"
              @save-design="handleSaveDesign"
              @share-design="handleShareDesign"
            />
          </aside>
        </div>
      </div>
    </main>

    <!-- Error Modal -->
    <div v-if="error" class="error-modal" @click="closeError">
      <div class="error-content" @click.stop>
        <h3>Configuration Error</h3>
        <p>{{ error }}</p>
        <button @click="closeError" class="btn btn-primary">Close</button>
      </div>
    </div>
  </div>
</template>

<script>
import EmbodeeConfigurator from './components/EmbodeeConfigurator.vue'
import CustomizationPanel from './components/CustomizationPanel.vue'
import ProductInfoPanel from './components/ProductInfoPanel.vue'

export default {
  name: 'App',
  components: {
    EmbodeeConfigurator,
    CustomizationPanel,
    ProductInfoPanel
  },
  data() {
    return {
      // Configuration
      workspaceId: 'qcxwobokwv', // From your sandbox URL
      productCode: 'Tee00198468', // From your sandbox URL
      designId: null,
      variant: null,
      quality: 'high',
      bgColor: null,
      
      // State
      loading: true,
      loadProgress: 0,
      error: null,
      
      // Data
      uiStructure: null,
      library: null,
      configurator: null,
      
      // Product Data
      productData: {
        title: 'WOMEN\'S NIKE DIGITAL HYPERELITE SHORT SLEEVE JERSEY',
        subtitle: 'High-performance athletic wear with customizable design options',
        style: 'HQ6389',
        lastOrderDate: '07/15/2029',
        description: 'Experience the future of apparel customization with our advanced 3D configurator. Create unique designs with real-time preview, choose from hundreds of colors and graphics, and see your vision come to life instantly. Perfect for teams, organizations, and individuals who want to stand out.'
      },
      
      // Customization tracking
      customizationSummary: {}
    }
  },
  mounted() {
    // Initialize the application
    this.initializeApp()
  },
  methods: {
    initializeApp() {
      console.log('Initializing Embodee Configurator App')
      // The configurator will be initialized by the EmbodeeConfigurator component
    },

    // Configurator Event Handlers
    handleConfiguratorReady(configurator) {
      console.log('Configurator ready:', configurator)
      this.configurator = configurator
    },

    handleProductReady(data) {
      console.log('Product ready:', data)
      this.uiStructure = data.uiStructure
      this.library = data.library
      this.loading = false
      this.loadProgress = 100
    },

    handleConfiguratorError(error) {
      console.error('Configurator error:', error)
      this.error = error.message || 'An error occurred with the configurator'
      this.loading = false
    },

    handleLoadStart() {
      console.log('Load started')
      this.loading = true
      this.loadProgress = 0
    },

    handleLoadProgress(percent) {
      console.log('Load progress:', percent)
      this.loadProgress = percent
    },

    handleLoadEnd() {
      console.log('Load ended')
      this.loadProgress = 100
    },

    handleMeshSelected(data) {
      console.log('Mesh selected:', data)
      // Handle mesh selection if needed
    },

    handleComponentValueChanged(data) {
      console.log('Component value changed:', data)
      // Update customization summary
      this.updateCustomizationSummary(data)
    },

    // Customization Handlers
    handleCustomizationChange(change) {
      console.log('Customization change:', change)
      
      if (this.configurator) {
        // Apply the customization to the configurator
        this.applyCustomization(change)
      }
      
      // Update customization summary
      this.updateCustomizationSummary(change)
    },

    handleTextPropertyChange(change) {
      console.log('Text property change:', change)
      
      if (this.configurator) {
        // Apply text property changes
        this.configurator.setComponentValue(
          change.elementId,
          change.property,
          change.value,
          false
        )
      }
    },

    applyCustomization(change) {
      // This would need to be implemented based on your specific product structure
      // For now, we'll log the change
      console.log('Applying customization:', change)
    },

    updateCustomizationSummary(change) {
      if (change.type) {
        this.customizationSummary[change.type] = change.value
      } else if (change.code && change.property) {
        this.customizationSummary[`${change.code}_${change.property}`] = change.value
      }
    },

    // Product Action Handlers
    handleAddToCart(data) {
      console.log('Add to cart:', data)
      // Implement add to cart functionality
      alert('Add to cart functionality would be implemented here')
    },

    handleSaveDesign(data) {
      console.log('Save design:', data)
      // Implement save design functionality
      alert('Save design functionality would be implemented here')
    },

    handleShareDesign(data) {
      console.log('Share design:', data)
      // Implement share design functionality
      alert('Share design functionality would be implemented here')
    },

    // Utility Methods
    closeError() {
      this.error = null
    }
  }
}
</script>

<style scoped>
#app {
  min-height: 100vh;
  background-color: #f5f5f5;
}

.app-header {
  background: white;
  border-bottom: 1px solid #e5e7eb;
  padding: 20px 0;
  margin-bottom: 20px;
}

.app-title {
  font-size: 28px;
  font-weight: 700;
  color: #111827;
  margin-bottom: 4px;
}

.app-subtitle {
  font-size: 16px;
  color: #6b7280;
  margin: 0;
}

.app-main {
  padding-bottom: 40px;
}

.customization-sidebar {
  height: fit-content;
}

.product-viewer-section {
  position: relative;
  min-height: 600px;
}

.product-viewer {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  min-height: 600px;
  position: relative;
}

.loading-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(255, 255, 255, 0.95);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
}

.loading-content {
  text-align: center;
  color: #6b7280;
}

.loading-content p {
  margin: 12px 0;
  font-size: 16px;
  font-weight: 500;
}

.progress-bar {
  width: 200px;
  height: 4px;
  background: #e5e7eb;
  border-radius: 2px;
  overflow: hidden;
  margin-top: 8px;
}

.progress-fill {
  height: 100%;
  background: #3b82f6;
  transition: width 0.3s ease;
}

.product-info-sidebar {
  height: fit-content;
}

.error-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.error-content {
  background: white;
  padding: 24px;
  border-radius: 8px;
  max-width: 400px;
  text-align: center;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
}

.error-content h3 {
  color: #dc2626;
  margin-bottom: 12px;
  font-size: 18px;
  font-weight: 600;
}

.error-content p {
  color: #6b7280;
  margin-bottom: 20px;
  line-height: 1.5;
}

/* Responsive Design */
@media (max-width: 1024px) {
  .grid-3 {
    grid-template-columns: 1fr;
    gap: 16px;
  }
  
  .customization-sidebar,
  .product-info-sidebar {
    order: 2;
  }
  
  .product-viewer-section {
    order: 1;
    min-height: 500px;
  }
  
  .product-viewer {
    min-height: 500px;
  }
}

@media (max-width: 768px) {
  .app-header {
    padding: 16px 0;
  }
  
  .app-title {
    font-size: 24px;
  }
  
  .app-subtitle {
    font-size: 14px;
  }
  
  .product-viewer {
    min-height: 400px;
  }
  
  .loading-content p {
    font-size: 14px;
  }
  
  .progress-bar {
    width: 150px;
  }
}
</style>
