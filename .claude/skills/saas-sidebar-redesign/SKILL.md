---
name: saas-sidebar-redesign
description: Redesign the Vue 3 app layout from a horizontal top nav bar to a modern SaaS-style fixed vertical sidebar with dark background, nav icons, and user profile section at the bottom.
---

# SaaS Sidebar Redesign

This skill guides the complete layout transformation of the Catalyst Components inventory management app from a horizontal top-nav layout to a modern SaaS-style vertical sidebar layout.

## Scope of Changes

Exactly two files change:

1. `client/src/App.vue` — template structure and layout CSS only. The entire `<script>` block is preserved verbatim. All global (unscoped) CSS utility classes are preserved verbatim. Only layout-related CSS classes are replaced.
2. `client/src/components/FilterBar.vue` — remove `position: sticky; top: 70px` from `.filters-bar` scoped style. No template or script changes.

No other files change. Do not touch any view files, composables, router config, or other components.

---

## Target Layout

```
┌──────────────────────────────────────────────────────────────────┐
│ .sidebar (fixed, 240px wide, full height, #1e293b bg)            │
│                                                                  │
│  .sidebar-logo                                                   │
│    company name                                                  │
│    subtitle                                                      │
│  ──────────────────────────                                      │
│  .sidebar-nav                                                    │
│    [icon] Overview                                               │
│    [icon] Inventory                                              │
│    [icon] Orders                                                 │
│    [icon] Finance                                                │
│    [icon] Demand Forecast                                        │
│    [icon] Reports                                                │
│  ──────────────────────────                                      │
│  .sidebar-footer                                                 │
│    <LanguageSwitcher />                                          │
│    <ProfileMenu />   (dropdown opens upward)                     │
└──────────────────────────────────────────────────────────────────┘

.app-body (flex: 1, margin-left: 240px, flex-direction: column)
  <FilterBar />    ← sticky top: 0 within this column
  .main-content
    <router-view />
```

---

## Section 1: App.vue — New Template

Replace the entire `<template>` block. Do not change anything in `<script>`.

```vue
<template>
  <div class="app">
    <!-- Fixed vertical sidebar -->
    <aside class="sidebar">
      <!-- Branding -->
      <div class="sidebar-logo">
        <h1>{{ t('nav.companyName') }}</h1>
        <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
      </div>

      <!-- Primary navigation -->
      <nav class="sidebar-nav">
        <router-link
          to="/"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/' }"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <circle cx="10" cy="10" r="7" stroke="currentColor" stroke-width="1.5"/>
            <path d="M10 10L6.5 6.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <circle cx="10" cy="10" r="1.5" fill="currentColor"/>
            <path d="M10 5v1.5M15 10h-1.5M10 15v-1.5M5 10h1.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>

        <router-link
          to="/inventory"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/inventory' }"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect x="3" y="9" width="14" height="8" rx="1" stroke="currentColor" stroke-width="1.5"/>
            <path d="M1 9l9-6 9 6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <rect x="7" y="13" width="6" height="4" rx="0.5" stroke="currentColor" stroke-width="1.5"/>
          </svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link
          to="/orders"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/orders' }"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect x="4" y="3" width="12" height="14" rx="1.5" stroke="currentColor" stroke-width="1.5"/>
            <path d="M7 7h6M7 10h6M7 13h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>

        <router-link
          to="/spending"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/spending' }"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <circle cx="10" cy="10" r="7" stroke="currentColor" stroke-width="1.5"/>
            <path d="M10 4.5v1M10 14.5v1" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <path d="M7.5 7.5h4a1.5 1.5 0 010 3H8.5a1.5 1.5 0 000 3H13" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>

        <router-link
          to="/demand"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/demand' }"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M3 14l4-5 3 3 3-4 4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M3 17h14" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link
          to="/reports"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/reports' }"
        >
          <svg class="nav-icon" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M5 3h7l4 4v10a1 1 0 01-1 1H5a1 1 0 01-1-1V4a1 1 0 011-1z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M12 3v4h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M7 10h6M7 13h4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>Reports</span>
        </router-link>
      </nav>

      <!-- Footer: language switcher + user profile -->
      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <!-- Main content column -->
    <div class="app-body">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <!-- Modals — unchanged -->
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
```

---

## Section 2: App.vue — Complete Replacement `<style>` Block

Replace the entire `<style>` block with the following. Every rule from `.page-header` onward is preserved exactly from the original; only the layout rules at the top are new.

```css
/* ============================================================
   RESET
   ============================================================ */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
    Cantarell, sans-serif;
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
  padding: 1.5rem 1.25rem 1.25rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  flex-shrink: 0;
}

.sidebar-logo h1 {
  font-size: 1rem;
  font-weight: 700;
  color: #f1f5f9;
  letter-spacing: -0.015em;
  line-height: 1.3;
}

.sidebar-subtitle {
  display: block;
  font-size: 0.75rem;
  color: #94a3b8;
  font-weight: 400;
  margin-top: 0.25rem;
  line-height: 1.4;
}

.sidebar-nav {
  flex: 1;
  padding: 0.75rem 0.75rem 0;
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.1) transparent;
}

.sidebar-nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.875rem;
  border-radius: 8px;
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
  background: rgba(37, 99, 235, 0.25);
  color: #93c5fd;
}

.sidebar-nav-item.active .nav-icon {
  color: #60a5fa;
}

.nav-icon {
  width: 18px;
  height: 18px;
  flex-shrink: 0;
  color: inherit;
}

.sidebar-footer {
  flex-shrink: 0;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

/* ============================================================
   APP BODY (right of sidebar)
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
   SIDEBAR COMPONENT OVERRIDES
   Adapts ProfileMenu and LanguageSwitcher for dark sidebar bg.
   Uses .sidebar ancestor to scope these overrides.
   ============================================================ */

/* ProfileMenu — dark button style */
.sidebar .profile-menu .profile-button {
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.12);
  color: #e2e8f0;
  width: 100%;
  justify-content: flex-start;
}

.sidebar .profile-menu .profile-button:hover {
  background: rgba(255, 255, 255, 0.07);
  border-color: rgba(255, 255, 255, 0.2);
}

.sidebar .profile-menu .profile-name {
  color: #e2e8f0;
}

.sidebar .profile-menu .chevron {
  color: #94a3b8;
}

/* ProfileMenu dropdown opens upward from sidebar footer */
.sidebar .profile-menu .dropdown-menu {
  bottom: calc(100% + 0.5rem);
  top: auto;
  right: auto;
  left: 0;
  min-width: 220px;
}

/* LanguageSwitcher — dark button style */
.sidebar .language-switcher .language-button {
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.12);
  color: #94a3b8;
  width: 100%;
  justify-content: flex-start;
  font-size: 0.8125rem;
}

.sidebar .language-switcher .language-button:hover {
  background: rgba(255, 255, 255, 0.07);
  border-color: rgba(255, 255, 255, 0.2);
  color: #e2e8f0;
}

/* LanguageSwitcher dropdown opens upward from sidebar footer */
.sidebar .language-switcher .dropdown-menu {
  bottom: calc(100% + 0.5rem);
  top: auto;
  left: 0;
  right: auto;
}

/* ============================================================
   PAGE HEADER — preserved from original
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
   STATS GRID — preserved from original
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
  border-radius: 10px;
  border: 1px solid #e2e8f0;
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
}

.stat-label {
  color: #64748b;
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
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
   CARD — preserved from original
   ============================================================ */
.card {
  background: white;
  border-radius: 10px;
  padding: 1.25rem;
  border: 1px solid #e2e8f0;
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid #e2e8f0;
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

/* ============================================================
   TABLE — preserved from original
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
  border-top: 1px solid #e2e8f0;
  border-bottom: 1px solid #e2e8f0;
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #475569;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid #f1f5f9;
  color: #334155;
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: #f8fafc;
}

/* ============================================================
   BADGE — preserved from original
   ============================================================ */
.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
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
   STATE HELPERS — preserved from original
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
```

---

## Section 3: FilterBar.vue — Remove Sticky Positioning

Open `client/src/components/FilterBar.vue`. In `<style scoped>`, change `.filters-bar`:

```css
/* BEFORE */
.filters-bar {
  background: #f8fafc;
  border-bottom: 1px solid #e2e8f0;
  padding: 0.75rem 0;
  position: sticky;
  top: 70px;
  z-index: 90;
}

/* AFTER — sticky relative to the app-body scroll container */
.filters-bar {
  background: #f8fafc;
  border-bottom: 1px solid #e2e8f0;
  padding: 0.75rem 0;
  position: sticky;
  top: 0;
  z-index: 90;
}
```

`top: 0` is correct because there is no longer a fixed top nav consuming vertical space. No other changes to FilterBar.vue.

---

## Section 4: Implementation Steps

Follow in order to avoid breaking the running dev server mid-edit.

1. **FilterBar.vue** — change `top: 70px` to `top: 0` in `.filters-bar`. Save.
2. **App.vue template** — select and delete the entire `<template>` block. Paste the new template from Section 1. Save.
3. **App.vue styles** — select and delete the entire `<style>` block. Paste the complete style block from Section 2. Save.
4. Verify in browser (see Section 5).

The `<script>` block in App.vue is never touched. All composable imports, component registrations, refs, and methods remain identical.

---

## Section 5: Verification Checklist

Open `http://localhost:3000` and confirm:

**Layout**
- [ ] Dark sidebar (~240px) appears fixed on the left
- [ ] Scrolling main content does NOT scroll the sidebar
- [ ] No horizontal scrollbar at default zoom
- [ ] Content area fills the space to the right of the sidebar

**Navigation**
- [ ] All 6 nav links visible with icons and labels
- [ ] Each link navigates to the correct route
- [ ] Active link shows blue-tinted background + lighter blue text
- [ ] Root `/` active state does NOT highlight all routes (explicit `:class` binding)

**Sidebar Footer**
- [ ] LanguageSwitcher button visible with dark styling
- [ ] ProfileMenu button visible showing user initials/name with dark styling
- [ ] ProfileMenu dropdown opens UPWARD (not clipped by viewport bottom)
- [ ] "Profile Details" menu item opens ProfileDetailsModal
- [ ] "My Tasks" menu item opens TasksModal with task list
- [ ] Logout item triggers the expected behavior

**Filter Bar**
- [ ] FilterBar appears at the top of the content column (not inside sidebar)
- [ ] All 4 filter selects functional
- [ ] Changing filters updates data on the active view
- [ ] Reset button enables when filters are active, disables when all = "all"

**i18n**
- [ ] Switching to Japanese changes all nav labels and UI text
- [ ] Switching back to English restores labels

**All Routes**
- [ ] `/` — Dashboard KPI cards and charts render
- [ ] `/inventory` — Inventory table renders with data
- [ ] `/orders` — Orders table renders with data
- [ ] `/spending` — Finance/spending charts render
- [ ] `/demand` — Demand forecast renders
- [ ] `/reports` — Reports page renders

---

## Section 6: Common Pitfalls

**Dropdown clipped at bottom of viewport** — ProfileMenu and LanguageSwitcher default to `top: calc(100% + 0.5rem)` in their scoped styles. The unscoped overrides in Section 2 use `bottom: calc(100% + 0.5rem); top: auto` to flip them upward. Three-class selector specificity (0,3,0) beats the scoped single-class selector (0,1,0).

**Content hidden behind sidebar** — `.app-body` must have `margin-left: 240px` matching `.sidebar` `width: 240px`. The sidebar uses `position: fixed`, removing it from normal flow, so a flex sibling would collapse to zero; margin-left is the correct fix.

**Script block accidentally modified** — Do not touch `<script>`. It exports `t`, `showProfileDetails`, `showTasks`, `tasks`, `addTask`, `deleteTask`, `toggleTask` — all referenced identically in the new template.

**Global CSS accidentally deleted** — App.vue's `<style>` block is unscoped. Rules like `.card`, `.badge`, `.stat-card`, table styles are consumed by all views. Use the complete style block in Section 2; do not remove anything after `.main-content`.

**FilterBar top value** — Use `top: 0` (not `top: 70px`) since there is no fixed top nav in the new layout.

**router-link active class** — The explicit `:class="{ active: $route.path === '/' }"` binding is intentional. Vue Router's `router-link-active` is non-exact and would match `/` on every route. Keep the explicit bindings on all 6 nav items.

---

## Section 7: Design Tokens

| Token         | Value                    | Usage                                    |
|---------------|--------------------------|------------------------------------------|
| Sidebar bg    | `#1e293b`                | Sidebar background (slate-800)           |
| Inactive text | `#94a3b8`                | Nav item text and icons (slate-400)      |
| Active text   | `#93c5fd`                | Active nav item text (blue-300)          |
| Active bg     | `rgba(37, 99, 235, 0.25)`| Active item fill (blue-600 at 25%)       |
| Divider       | `rgba(255,255,255,0.08)` | Sidebar section separators               |
| Page bg       | `#f8fafc`                | Content area background (slate-50)       |
| Primary text  | `#1e293b`                | Body copy (slate-800)                    |
| Muted text    | `#64748b`                | Labels, subtitles (slate-500)            |
| Border        | `#e2e8f0`                | Card and table borders (slate-200)       |
| Brand blue    | `#2563eb`                | Active states and focus rings (blue-600) |

---

## Section 8: What Is NOT Changed

- `client/src/App.vue` — `<script>` block (all composables, refs, methods, lifecycle hooks)
- `client/src/components/FilterBar.vue` — template, script, all CSS except `top` value
- `client/src/components/ProfileMenu.vue` — no changes
- `client/src/components/LanguageSwitcher.vue` — no changes
- `client/src/components/ProfileDetailsModal.vue` — no changes
- `client/src/components/TasksModal.vue` — no changes
- `client/src/composables/*` — no changes
- `client/src/views/*` — no changes
- `client/src/main.js` — no changes
- `server/*` — no changes
