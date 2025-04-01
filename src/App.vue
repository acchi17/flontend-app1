<!-- App.vue -->
<template>
  <div class="app-container">
    <header class="app-header">
      <div class="logo">
        <span class="logo-text">COMPANY</span>
        <span class="logo-subtitle">Image Gallery</span>
      </div>
      <div class="header-actions">
        <button class="header-button">Login</button>
      </div>
    </header>
    <div class="content-wrapper">
      <div class="sidebar-container">
        <Sidebar :activeMenu="activeMenu" @menu-click="handleMenuClick" />
      </div>
      <div class="main-container">
        <div v-if="loading" class="loading">
          <p>Loading data...</p>
        </div>
        <div v-else-if="error" class="error">
          <p>An error occurred: {{ error }}</p>
          <button @click="fetchData">Retry</button>
        </div>
        <div v-else-if="items.length === 0" class="no-items">
          <p>No data to display.</p>
        </div>
        <div v-else class="card-grid">
          <div v-for="(item, index) in items" :key="index" class="card-item">
            <div class="card-image">
              <img :src="item.imageUrl" :alt="item.summary || `Item ${index + 1}`" @error="handleImageError(index)" />
            </div>
            <div class="card-content">
              <h3 class="card-title">{{ item.summary }}</h3>
              <p class="card-detail">{{ item.detail }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from './components/Sidebar.vue'

export default {
  name: 'ImageGallery',
  components: {
    Sidebar
  },
  data() {
    return {
      items: [],
      loading: true,
      error: null,
      imageErrors: new Set(),
      activeMenu: 'gallery'
    }
  },
  mounted() {
    this.fetchData()
  },
  methods: {
    handleMenuClick(menu) {
      this.activeMenu = menu
      // Execute actions corresponding to each menu item as needed
      if (menu === 'gallery') {
        this.fetchData()
      }
    },
    async fetchData() {
      this.loading = true
      this.error = null
      
      try {
        // Get backend URL from environment variables
        let backendUrl
        
        // Use complete URL if specified
        if (process.env.VUE_APP_BACKEND_URL) {
          backendUrl = process.env.VUE_APP_BACKEND_URL
        } else {
          // Construct from individual elements
          const backendProtocol = process.env.VUE_APP_BACKEND_PROTOCOL || 'http'
          const backendHost = process.env.VUE_APP_BACKEND_HOST || 'localhost'
          const backendPort = process.env.VUE_APP_BACKEND_PORT || '3000'
          backendUrl = `${backendProtocol}://${backendHost}:${backendPort}`
        }
        
        // Remove trailing slash if present
        backendUrl = backendUrl.replace(/\/$/, '')
        
        // Fetch data from backend endpoint
        const response = await fetch(`${backendUrl}/api/data`)
        
        if (!response.ok) {
          throw new Error(`API request failed: ${response.status} ${response.statusText}`)
        }
        
        const data = await response.json()
        
        // Check if the data format is as expected
        if (!Array.isArray(data)) {
          throw new Error('Data returned from API is not an array')
        }
        
        // Convert data to appropriate object format
        this.items = data.map(item => {
          // Adjust based on the format of data returned from the backend
          if (typeof item === 'object' && item !== null) {
            return {
              imageUrl: item.imageUrl || '',
              summary: item.summary || '',
              detail: item.detail || ''
            }
          }
          return { imageUrl: '', summary: '', detail: '' } // Fallback
        }).filter(item => item.imageUrl) // Exclude items with empty image URL
        
      } catch (err) {
        console.error('Error fetching data:', err)
        this.error = err.message
      } finally {
        this.loading = false
      }
    },
    handleImageError(index) {
      // Handle image loading error
      this.imageErrors.add(index)
      this.items[index].error = true
      console.warn(`Failed to load image: ${this.items[index].imageUrl}`)
    }
  }
}
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100%;
}

.content-wrapper {
  display: flex;
  flex: 1;
  overflow: hidden;
}

.sidebar-container {
  width: 250px;
  height: 100%;
  overflow: auto;
}

.main-container {
  flex: 1;
  padding: 20px;
  overflow: auto;
}

.app-header {
  background-color: #fff;
  border-bottom: 1px solid #e1e4e8;
  padding: 15px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.logo {
  display: flex;
  flex-direction: column;
}

.logo-text {
  font-size: 24px;
  font-weight: bold;
  color: #2c3e50;
  letter-spacing: 1px;
}

.logo-subtitle {
  font-size: 14px;
  color: #7f8c8d;
}

.header-actions {
  display: flex;
  align-items: center;
}

.header-button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.header-button:hover {
  background-color: #2980b9;
}

.main-container {
  flex: 1;
  padding: 20px;
  overflow: auto;
}

.loading, .error, .no-items {
  text-align: center;
  padding: 40px;
  font-size: 18px;
}

.error {
  color: #e74c3c;
}

button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  margin-top: 10px;
}

button:hover {
  background-color: #2980b9;
}

.card-grid {
  display: flex;
  flex-direction: column;
  gap: 20px;
  max-width: 800px;
  margin: 0 auto;
}

.card-item {
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
  background-color: white;
  width: 100%;
}

.card-item:hover {
  transform: translateY(-5px);
}

.card-image img {
  width: 100%;
  height: 300px;
  object-fit: cover;
  display: block;
}

.card-content {
  padding: 15px;
}

.card-title {
  margin: 0 0 10px 0;
  font-size: 18px;
  color: #333;
}

.card-detail {
  margin: 0;
  font-size: 14px;
  color: #666;
  line-height: 1.4;
}

.no-items {
  text-align: center;
  padding: 40px;
  font-size: 18px;
}
</style>
