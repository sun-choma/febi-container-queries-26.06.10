<template>
  <div class="demo-root">
    <!-- the only state holder — no JS -->
    <input id="sb-toggle" type="checkbox" class="sb-toggle"/>

    <div class="app">
      <!-- Sidebar: a container. NavItems inside query its width. -->
      <aside class="sidebar">
        <label for="sb-toggle" class="menu-btn">
          <Menu :size="18"/>
          <span class="menu-label">Menu</span>
        </label>

        <nav class="side-nav">
          <NavItem
              v-for="item in items"
              :key="item.label"
              :icon="item.icon"
              :label="item.label"
              :desc="item.desc"
              :active="item.active"
          />
        </nav>
      </aside>

      <!-- Content: 2-col grid, each cell its own container holding the SAME NavItem. -->
      <main class="content">
        <div class="card-grid">
          <div class="card-cell" v-for="item in items" :key="item.label">
            <NavItem
                :icon="item.icon"
                :label="item.label"
                :desc="item.desc"
                :active="item.active"
            />
          </div>
        </div>
      </main>
    </div>

    <div class="hint">サイドバーのメニューボタンを押してみてください</div>
  </div>
</template>

<script setup>
import {House, ChartColumn, Folder, Settings, Users, Bell, Menu} from '@lucide/vue'
import NavItem from './NavItem.vue'

const items = [
  {icon: House, label: 'Home', desc: 'Go to dashboard', active: false},
  {icon: ChartColumn, label: 'Analytics', desc: 'View your stats', active: true},
  {icon: Folder, label: 'Projects', desc: 'All your work', active: false},
  {icon: Users, label: 'Team', desc: 'Manage members', active: false},
  {icon: Bell, label: 'Alerts', desc: 'Recent notifications', active: false},
  {icon: Settings, label: 'Settings', desc: 'Preferences', active: false},
]
</script>

<style scoped>
.demo-root {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.6rem;
  font-family: 'Fira Code', monospace;
}

.sb-toggle {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

.app {
  display: flex;
  width: 660px;
  height: 300px;
  background: #1e1f29;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  overflow: hidden;
}

/* ── Sidebar (container) ── */
.sidebar {
  flex: 0 0 auto;
  width: 48px; /* collapsed */
  background: #16171e;
  border-right: 1px solid rgba(255, 255, 255, 0.07);
  padding: 0.6rem 0.5rem;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
  transition: width 0.3s ease;
  container-type: inline-size;
  overflow-y: auto;
}

.sb-toggle:checked ~ .app .sidebar {
  width: 180px;
}

.menu-btn {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.4rem 0.5rem;
  border-radius: 7px;
  color: #f8f8f2;
  cursor: pointer;
  user-select: none;
  transition: background 0.15s;
  flex-shrink: 0;

  & > svg {
    flex-shrink: 0;
  }
}

.menu-btn:hover {
  background: rgba(255, 255, 255, 0.06);
}

.menu-label {
  font-size: 0.8rem;
  white-space: nowrap;
  opacity: 0;
  transition: opacity 0.2s;
}

.side-nav {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

/* show the menu word once the sidebar is wide enough */
@container (min-width: 120px) {
  .menu-label {
    opacity: 1;
  }
}

/* ── Content ── */
.content {
  flex: 1;
  min-width: 0;
  padding: 0.9rem;
  overflow-y: auto;
}

.card-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.6rem;
}

/* each cell is its own container — the SAME NavItem adapts independently */
.card-cell {
  container-type: inline-size;
  background: #252631;
  border: 1px solid rgba(255, 255, 255, 0.06);
  border-radius: 9px;
}

.hint {
  font-size: 0.65rem;
  color: rgba(255, 255, 255, 0.35);
  letter-spacing: 0.03em;
}
</style>