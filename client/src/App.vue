<template>
  <div class="app">
    <!-- Fixed vertical sidebar -->
    <aside :class="['sidebar', { collapsed: sidebarCollapsed }]">
      <!-- Branding -->
      <div class="sidebar-logo">
        <div class="sidebar-logo-content">
          <h1>{{ t('nav.companyName') }}</h1>
          <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
        </div>
        <button class="sidebar-toggle" @click="toggleSidebar" :title="sidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'">
          <svg viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path v-if="!sidebarCollapsed" d="M13 5l-5 5 5 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path v-else d="M7 5l5 5-5 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </button>
      </div>

      <!-- Primary navigation -->
      <nav class="sidebar-nav">
        <router-link to="/" class="sidebar-nav-item" :class="{ active: $route.path === '/' }" title="Overview">
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <circle cx="10" cy="10" r="7" stroke="currentColor" stroke-width="1.5"/>
            <path d="M10 10L6.5 6.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <circle cx="10" cy="10" r="1.5" fill="currentColor"/>
            <path d="M10 5v1.5M15 10h-1.5M10 15v-1.5M5 10h1.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>

        <router-link to="/inventory" class="sidebar-nav-item" :class="{ active: $route.path === '/inventory' }" title="Inventory">
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect x="3" y="9" width="14" height="8" rx="1" stroke="currentColor" stroke-width="1.5"/>
            <path d="M1 9l9-6 9 6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <rect x="7" y="13" width="6" height="4" rx="0.5" stroke="currentColor" stroke-width="1.5"/>
          </svg>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link to="/orders" class="sidebar-nav-item" :class="{ active: $route.path === '/orders' }" title="Orders">
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect x="4" y="3" width="12" height="14" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
            <path d="M7 7h6M7 10h6M7 13h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>

        <router-link to="/spending" class="sidebar-nav-item" :class="{ active: $route.path === '/spending' }" title="Finance">
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <circle cx="10" cy="10" r="7" stroke="currentColor" stroke-width="1.5"/>
            <path d="M10 4.5v1M10 14.5v1" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <path d="M7.5 7.5h4a1.5 1.5 0 010 3H8.5a1.5 1.5 0 000 3H13" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>

        <router-link to="/demand" class="sidebar-nav-item" :class="{ active: $route.path === '/demand' }" title="Demand Forecast">
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M3 14l4-5 3 3 3-4 4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M3 17h14" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link to="/reports" class="sidebar-nav-item" :class="{ active: $route.path === '/reports' }" title="Reports">
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M5 3h7l4 4v10a1 1 0 01-1 1H5a1 1 0 01-1-1V4a1 1 0 011-1z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M12 3v4h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M7 10h6M7 13h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span class="nav-label">Reports</span>
        </router-link>
      </nav>

      <!-- Filters section -->
      <div class="sidebar-filters-section">
        <div class="sidebar-filters-title">Filters</div>
        <FilterBar />
      </div>

      <!-- Footer: language + user -->
      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <!-- Main content -->
    <div class="app-body" :style="{ marginLeft: sidebarCollapsed ? '64px' : '240px' }">
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <!-- Modals -->
    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />
    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, computed, onUnmounted } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const apiTasks = ref([])

    const sidebarCollapsed = ref(false)

    const toggleSidebar = () => {
      sidebarCollapsed.value = !sidebarCollapsed.value
    }

    // Auto-collapse on small screens
    const mediaQuery = window.matchMedia('(max-width: 1023px)')
    const handleMediaChange = (e) => {
      sidebarCollapsed.value = e.matches
    }

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(() => {
      loadTasks()
      sidebarCollapsed.value = mediaQuery.matches
      mediaQuery.addEventListener('change', handleMediaChange)
    })

    onUnmounted(() => {
      mediaQuery.removeEventListener('change', handleMediaChange)
    })

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      sidebarCollapsed,
      toggleSidebar
    }
  }
}
</script>

<style>
/* ============================================================
   RESET
   ============================================================ */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: #f8fafc;
  color: #1e293b;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* ============================================================
   ROOT LAYOUT
   ============================================================ */
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
}

/* ============================================================
   SIDEBAR
   ============================================================ */
.sidebar {
  width: 240px;
  min-width: 240px;
  background: #1e293b;
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  z-index: 100;
  overflow: hidden;
}

.sidebar-logo {
  padding: 1.375rem 1.25rem 1.125rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  flex-shrink: 0;
}

.sidebar-logo h1 {
  font-size: 0.9375rem;
  font-weight: 700;
  color: #f1f5f9;
  letter-spacing: -0.015em;
  line-height: 1.3;
}

.sidebar-subtitle {
  display: block;
  font-size: 0.7rem;
  color: #64748b;
  font-weight: 400;
  margin-top: 0.2rem;
  line-height: 1.4;
}

.sidebar-nav {
  padding: 0.625rem 0.625rem 0.5rem;
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  flex-shrink: 0;
}

.sidebar-nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.5rem 0.75rem;
  border-radius: 7px;
  color: #94a3b8;
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  transition: background 0.15s ease, color 0.15s ease;
  white-space: nowrap;
}

.sidebar-nav-item:hover {
  background: rgba(255, 255, 255, 0.07);
  color: #e2e8f0;
}

.sidebar-nav-item.active {
  background: rgba(37, 99, 235, 0.2);
  color: #93c5fd;
}

.sidebar-nav-item.active .nav-icon {
  color: #60a5fa;
}

.nav-icon {
  width: 17px;
  height: 17px;
  flex-shrink: 0;
  color: inherit;
}

/* Filters section */
.sidebar-filters-section {
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  flex-shrink: 0;
}

.sidebar-filters-title {
  padding: 0.625rem 0.75rem 0;
  font-size: 0.65rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: #475569;
}

/* Footer */
.sidebar-footer {
  flex-shrink: 0;
  padding: 0.625rem;
  display: flex;
  flex-direction: column;
  gap: 0.375rem;
  margin-top: auto;
}

/* ============================================================
   SIDEBAR TOGGLE BUTTON
   ============================================================ */
.sidebar-logo {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 0.5rem;
}

.sidebar-logo-content {
  flex: 1;
  min-width: 0;
}

.sidebar-toggle {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  color: #64748b;
  cursor: pointer;
  transition: all 0.15s;
  margin-top: 0.125rem;
}

.sidebar-toggle:hover {
  background: rgba(255, 255, 255, 0.08);
  color: #94a3b8;
}

.sidebar-toggle svg {
  width: 14px;
  height: 14px;
}

/* ============================================================
   SIDEBAR COLLAPSED STATE
   ============================================================ */
.sidebar {
  transition: width 0.2s ease;
}

.sidebar.collapsed {
  width: 64px;
  min-width: 64px;
}

/* In collapsed mode: center nav items, hide labels */
.sidebar.collapsed .sidebar-nav-item {
  justify-content: center;
  padding: 0.5rem;
  gap: 0;
}

.sidebar.collapsed .nav-label {
  display: none;
}

/* In collapsed mode: hide subtitle */
.sidebar.collapsed .sidebar-subtitle {
  display: none;
}

/* In collapsed mode: shrink logo area, center icon */
.sidebar.collapsed .sidebar-logo {
  justify-content: center;
  padding: 1rem 0.625rem;
}

.sidebar.collapsed .sidebar-logo-content {
  display: none;
}

.sidebar.collapsed .sidebar-toggle {
  margin-top: 0;
}

/* In collapsed mode: hide filters section and its title */
.sidebar.collapsed .sidebar-filters-section {
  display: none;
}

/* In collapsed mode: center footer items */
.sidebar.collapsed .sidebar-footer {
  align-items: center;
  padding: 0.625rem 0.5rem;
}

/* In collapsed mode: hide language switcher entirely */
.sidebar.collapsed .language-switcher {
  display: none;
}

/* In collapsed mode: profile button becomes icon-only */
.sidebar.collapsed .profile-menu .profile-button {
  justify-content: center;
  padding: 0.5rem;
  width: 40px;
  border-radius: 50%;
}

.sidebar.collapsed .profile-menu .profile-name,
.sidebar.collapsed .profile-menu .chevron {
  display: none;
}

/* Smooth margin transition on app body */
.app-body {
  transition: margin-left 0.2s ease;
}

/* ============================================================
   SIDEBAR COMPONENT OVERRIDES
   ============================================================ */

/* ProfileMenu — dark style */
.sidebar .profile-menu .profile-button {
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #e2e8f0;
  width: 100%;
  justify-content: flex-start;
  border-radius: 7px;
}

.sidebar .profile-menu .profile-button:hover {
  background: rgba(255, 255, 255, 0.07);
  border-color: rgba(255, 255, 255, 0.18);
}

.sidebar .profile-menu .profile-name {
  color: #cbd5e1;
  font-size: 0.8125rem;
}

.sidebar .profile-menu .chevron {
  color: #475569;
}

/* Dropdown opens upward */
.sidebar .profile-menu .dropdown-menu {
  bottom: calc(100% + 0.5rem);
  top: auto;
  right: auto;
  left: 0;
  min-width: 220px;
}

/* LanguageSwitcher — dark style */
.sidebar .language-switcher .language-button {
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #94a3b8;
  width: 100%;
  justify-content: flex-start;
  font-size: 0.8rem;
  border-radius: 7px;
}

.sidebar .language-switcher .language-button:hover {
  background: rgba(255, 255, 255, 0.07);
  border-color: rgba(255, 255, 255, 0.18);
  color: #e2e8f0;
}

.sidebar .language-switcher .globe-icon {
  color: #64748b;
}

/* Dropdown opens upward */
.sidebar .language-switcher .dropdown-menu {
  bottom: calc(100% + 0.5rem);
  top: auto;
  left: 0;
  right: auto;
}

/* ============================================================
   APP BODY
   ============================================================ */
.app-body {
  flex: 1;
  margin-left: 240px;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  overflow-x: hidden;
}

/* ============================================================
   MAIN CONTENT
   ============================================================ */
.main-content {
  flex: 1;
  max-width: 1600px;
  width: 100%;
  margin: 0 auto;
  padding: 1.5rem 2rem;
}

/* ============================================================
   PAGE HEADER
   ============================================================ */
.page-header {
  margin-bottom: 1.5rem;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: #0f172a;
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: #64748b;
  font-size: 0.938rem;
}

/* ============================================================
   STATS GRID
   ============================================================ */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: white;
  padding: 1.25rem;
  border-radius: 12px;
  border: 1px solid #e2e8f0;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.stat-label {
  color: #64748b;
  font-size: 0.8125rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value { color: #ea580c; }
.stat-card.success .stat-value { color: #059669; }
.stat-card.danger  .stat-value { color: #dc2626; }
.stat-card.info    .stat-value { color: #2563eb; }

/* ============================================================
   CARD
   ============================================================ */
.card {
  background: white;
  border-radius: 12px;
  padding: 1.25rem;
  border: 1px solid #e2e8f0;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid #f1f5f9;
}

.card-title {
  font-size: 1rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.02em;
}

/* ============================================================
   TABLE
   ============================================================ */
.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #f8fafc;
  border-top: 1px solid #f1f5f9;
  border-bottom: 1px solid #e2e8f0;
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #64748b;
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 0.07em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid #f8fafc;
  color: #334155;
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.12s ease;
}

tbody tr:hover {
  background: #f8fafc;
}

/* ============================================================
   BADGE
   ============================================================ */
.badge {
  display: inline-block;
  padding: 0.25rem 0.625rem;
  border-radius: 6px;
  font-size: 0.72rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.03em;
}

.badge.success    { background: #d1fae5; color: #065f46; }
.badge.warning    { background: #fed7aa; color: #92400e; }
.badge.danger     { background: #fecaca; color: #991b1b; }
.badge.info       { background: #dbeafe; color: #1e40af; }
.badge.increasing { background: #d1fae5; color: #065f46; }
.badge.decreasing { background: #fecaca; color: #991b1b; }
.badge.stable     { background: #e0e7ff; color: #3730a3; }
.badge.high       { background: #fecaca; color: #991b1b; }
.badge.medium     { background: #fed7aa; color: #92400e; }
.badge.low        { background: #dbeafe; color: #1e40af; }

/* ============================================================
   STATE HELPERS
   ============================================================ */
.loading {
  text-align: center;
  padding: 3rem;
  color: #64748b;
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: 1rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.938rem;
}
</style>
