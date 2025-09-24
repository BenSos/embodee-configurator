<template>
  <div class="product-info-panel">
    <div class="card">
      <!-- Product Title -->
      <div class="product-title-section">
        <h1 class="product-title">{{ productTitle }}</h1>
        <p class="product-subtitle">{{ productSubtitle }}</p>
      </div>

      <!-- Product Details -->
      <div class="product-details">
        <h3 class="details-title">Details</h3>
        <div class="details-content">
          <div class="detail-item">
            <span class="detail-label">Style #:</span>
            <span class="detail-value">{{ productStyle }}</span>
          </div>
          
          <div class="detail-item sustainable">
            <span class="sustainable-icon">🌱</span>
            <span class="detail-text">Sustainable Materials</span>
          </div>
          
          <div class="detail-item">
            <span class="detail-label">Last Date to Place New Orders:</span>
            <span class="detail-value">{{ lastOrderDate }}</span>
          </div>
        </div>
      </div>

      <!-- Product Description -->
      <div class="product-description">
        <h3 class="description-title">About This Product</h3>
        <p class="description-text">
          {{ productDescription }}
        </p>
      </div>

      <!-- Customization Summary -->
      <div class="customization-summary" v-if="customizationSummary">
        <h3 class="summary-title">Your Customization</h3>
        <div class="summary-content">
          <div 
            v-for="(value, key) in customizationSummary" 
            :key="key"
            class="summary-item"
          >
            <span class="summary-label">{{ formatLabel(key) }}:</span>
            <span class="summary-value">{{ value }}</span>
          </div>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="action-buttons">
        <button class="btn btn-primary full-width" @click="addToCart">
          Add to Cart
        </button>
        <button class="btn btn-secondary full-width" @click="saveDesign">
          Save Design
        </button>
        <button class="btn btn-secondary full-width" @click="shareDesign">
          Share Design
        </button>
      </div>

      <!-- Additional Features -->
      <div class="additional-features">
        <div class="feature-item">
          <span class="feature-icon">📱</span>
          <span class="feature-text">Mobile Optimized</span>
        </div>
        <div class="feature-item">
          <span class="feature-icon">🎨</span>
          <span class="feature-text">Real-time Preview</span>
        </div>
        <div class="feature-item">
          <span class="feature-icon">⚡</span>
          <span class="feature-text">Fast Loading</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductInfoPanel',
  props: {
    productData: {
      type: Object,
      default: () => ({
        title: 'WOMEN\'S NIKE DIGITAL HYPERELITE SHORT SLEEVE JERSEY',
        subtitle: 'High-performance athletic wear with customizable design options',
        style: 'HQ6389',
        lastOrderDate: '07/15/2029',
        description: 'Experience the future of apparel customization with our advanced 3D configurator. Create unique designs with real-time preview, choose from hundreds of colors and graphics, and see your vision come to life instantly. Perfect for teams, organizations, and individuals who want to stand out.'
      })
    },
    customizationSummary: {
      type: Object,
      default: null
    }
  },
  computed: {
    productTitle() {
      return this.productData.title || 'Product Title'
    },
    productSubtitle() {
      return this.productData.subtitle || 'Replace this title with "Product Title"'
    },
    productStyle() {
      return this.productData.style || 'N/A'
    },
    lastOrderDate() {
      return this.productData.lastOrderDate || 'N/A'
    },
    productDescription() {
      return this.productData.description || 'Replace this area with a short description on how the configurator can be implemented into your site easily and with any look or integration you would like.'
    }
  },
  methods: {
    formatLabel(key) {
      return key.replace(/([A-Z])/g, ' $1').replace(/^./, str => str.toUpperCase())
    },
    addToCart() {
      this.$emit('add-to-cart', {
        product: this.productData,
        customization: this.customizationSummary
      })
    },
    saveDesign() {
      this.$emit('save-design', {
        product: this.productData,
        customization: this.customizationSummary
      })
    },
    shareDesign() {
      this.$emit('share-design', {
        product: this.productData,
        customization: this.customizationSummary
      })
    }
  }
}
</script>

<style scoped>
.product-info-panel {
  height: fit-content;
}

.product-title-section {
  margin-bottom: 24px;
}

.product-title {
  font-size: 18px;
  font-weight: 700;
  color: #111827;
  line-height: 1.4;
  margin-bottom: 8px;
  text-transform: uppercase;
}

.product-subtitle {
  font-size: 14px;
  color: #6b7280;
  font-style: italic;
}

.product-details {
  margin-bottom: 24px;
  padding: 16px;
  background: #f9fafb;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
}

.details-title {
  font-size: 16px;
  font-weight: 600;
  color: #374151;
  margin-bottom: 12px;
}

.details-content {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.detail-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
}

.detail-label {
  font-weight: 500;
  color: #6b7280;
}

.detail-value {
  color: #374151;
  font-weight: 500;
}

.detail-item.sustainable {
  color: #059669;
  font-weight: 500;
}

.sustainable-icon {
  font-size: 16px;
}

.product-description {
  margin-bottom: 24px;
}

.description-title {
  font-size: 16px;
  font-weight: 600;
  color: #374151;
  margin-bottom: 12px;
}

.description-text {
  font-size: 14px;
  color: #6b7280;
  line-height: 1.6;
}

.customization-summary {
  margin-bottom: 24px;
  padding: 16px;
  background: #eff6ff;
  border-radius: 8px;
  border: 1px solid #bfdbfe;
}

.summary-title {
  font-size: 16px;
  font-weight: 600;
  color: #1e40af;
  margin-bottom: 12px;
}

.summary-content {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.summary-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 14px;
}

.summary-label {
  color: #1e40af;
  font-weight: 500;
}

.summary-value {
  color: #374151;
  font-weight: 600;
}

.action-buttons {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 24px;
}

.full-width {
  width: 100%;
  padding: 12px 16px;
  font-size: 14px;
  font-weight: 500;
}

.additional-features {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding-top: 16px;
  border-top: 1px solid #e5e7eb;
}

.feature-item {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: #6b7280;
}

.feature-icon {
  font-size: 16px;
}

.feature-text {
  font-weight: 500;
}

@media (max-width: 768px) {
  .product-title {
    font-size: 16px;
  }
  
  .action-buttons {
    gap: 8px;
  }
  
  .additional-features {
    flex-direction: row;
    flex-wrap: wrap;
    gap: 16px;
  }
}
</style>
