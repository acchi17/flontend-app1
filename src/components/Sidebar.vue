<!-- Sidebar.vue -->
<template>
  <div class="sidebar">
    <nav class="sidebar-nav">
      <ul class="vertical-nav">
        <li><a href="#" @click.prevent="$emit('menu-click', 'home')" :class="{ active: activeMenu === 'home' }">Home</a></li>
        <li><a href="#" @click.prevent="$emit('menu-click', 'gallery')" :class="{ active: activeMenu === 'gallery' }">Gallery</a></li>
        <li><a href="#" @click.prevent="$emit('menu-click', 'favorites')" :class="{ active: activeMenu === 'favorites' }">Favorites</a></li>
        <li><a href="#" @click.prevent="$emit('menu-click', 'settings')" :class="{ active: activeMenu === 'settings' }">Settings</a></li>
      </ul>
    </nav>
    <div class="sidebar-footer">
      <small>© 2025 Image Gallery</small>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AppSidebar',
  props: {
    activeMenu: {
      type: String,
      default: 'gallery'
    }
  }
}
</script>

<style scoped>
.sidebar {
  background-color: #2c3e50;
  color: white;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.sidebar-nav {
  flex-grow: 1;
  padding: 20px 0;
}

.sidebar-nav ul.vertical-nav {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
}

.sidebar-nav li {
  margin-bottom: 5px;
}

.sidebar-nav a {
  display: block;
  padding: 10px 20px;
  color: #ecf0f1;
  text-decoration: none;
  transition: background-color 0.3s;
}

.sidebar-nav a:hover, .sidebar-nav a.active {
  background-color: #34495e;
}

.sidebar-footer {
  padding: 5px 20px;
  font-size: 0.7rem;
  text-align: center;
  border-top: 1px solid #34495e;
  opacity: 0.7;
}
</style>
