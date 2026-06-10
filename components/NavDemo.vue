<template>
  <div class="devices">

    <!-- Mobile -->
    <div class="device-wrap">
      <div class="device-label">mobile</div>
      <div class="frame phone">
        <div class="mobile-layout">
          <div class="content-area">content area</div>
          <!-- bottom bar: short + wide → container-type: size, items query height -->
          <div class="bar bar-bottom">
            <nav class="nav nav-row">
              <div
                  v-for="item in items"
                  :key="item.label"
                  class="nav-item"
                  :class="{ active: item.active }"
              >
                <span class="nav-icon"><component :is="item.icon" :size="18"/></span>
                <span class="nav-label">{{ item.label }}</span>
                <span class="nav-desc">{{ item.desc }}</span>
              </div>
            </nav>
          </div>
        </div>
      </div>
    </div>

    <!-- Desktop -->
    <div class="device-wrap desktop-wrap">
      <div class="device-label">desktop</div>
      <div class="frame desktop">
        <div class="desktop-layout">
          <!-- sidebar: tall + variable width → container-type: inline-size, items query width -->
          <div class="bar bar-side">
            <span class="hint">← drag →</span>
            <nav class="nav nav-col">
              <div
                  v-for="item in items"
                  :key="item.label"
                  class="nav-item"
                  :class="{ active: item.active }"
              >
                <span class="nav-icon"><component :is="item.icon" :size="18"/></span>
                <span class="nav-label">{{ item.label }}</span>
                <span class="nav-desc">{{ item.desc }}</span>
              </div>
            </nav>
          </div>
          <div class="content-area">content area</div>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import {House, ChartColumn, Folder, Settings} from '@lucide/vue'

const items = [
  {icon: House, label: 'Home', desc: 'Go to dashboard', active: false},
  {icon: ChartColumn, label: 'Analytics', desc: 'View your performance metrics', active: true},
  {icon: Folder, label: 'Projects', desc: 'All your work', active: false},
  {icon: Settings, label: 'Settings', desc: 'Preferences', active: false},
]
</script>

<style scoped>
.devices {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  gap: 2.5rem;
  padding: 0.5rem 1rem;
  font-family: 'Fira Code', monospace;
}

.device-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.4rem;
}

.desktop-wrap {
  flex: 1;
}

.device-label {
  font-size: 0.6rem;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  opacity: 0.35;
}

.frame {
  border: 2px solid rgba(255, 255, 255, 0.12);
  border-radius: 14px;
  overflow: hidden;
}

.phone {
  width: 160px;
  height: 280px;
}

.desktop {
  width: 100%;
  height: 280px;
  border-radius: 10px;
}

/* ════════════════════════════════════════
   ONE nav-item rule set.
   Desktop sidebar exposes width  → min-width rules fire.
   Mobile bar exposes height      → min-height rules fire.
   ════════════════════════════════════════ */
.nav {
  display: flex;
}

.nav-row {
  flex-direction: row;
  justify-content: space-around;
}

.nav-col {
  flex-direction: column;
  gap: 2px;
}

/* base: icon only */
.nav-item {
  display: grid;
  grid-template-areas: "icon";
  grid-template-columns: auto;
  justify-items: center;
  align-items: center;
  gap: 0.15rem;
  padding: 0.5rem;
  border-radius: 7px;
  cursor: default;
  transition: background 0.15s;
}

.nav-item:hover {
  background: rgba(255, 255, 255, 0.05);
}

.nav-item.active {
  background: rgba(189, 147, 249, 0.15);
}

.nav-icon {
  grid-area: icon;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #f8f8f2;
}

.nav-item.active .nav-icon {
  color: #bd93f9;
}

.nav-label {
  grid-area: label;
  font-size: 0.7rem;
  color: #f8f8f2;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: none;
}

.nav-item.active .nav-label {
  color: #bd93f9;
}

.nav-desc {
  grid-area: desc;
  font-size: 0.62rem;
  color: rgba(255, 255, 255, 0.38);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: none;
}

/* ── WIDTH-driven (desktop sidebar, inline-size) ── */

/* medium width: icon + label side by side */
@container (min-width: 150px) {
  .nav-item {
    grid-template-areas: "icon label";
    grid-template-columns: 1.6rem 1fr;
    column-gap: 0.5rem;
    justify-items: start;
  }

  .nav-item .nav-label {
    display: block;
    font-size: 0.8rem;
  }
}

/* large width: icon + label + description */
@container (min-width: 230px) {
  .nav-item {
    grid-template-areas: "icon label" "icon desc";
    align-items: start;
    padding: 0.6rem 0.5rem;
  }

  .nav-item .nav-label {
    align-self: end;
    font-size: 0.85rem;
  }

  .nav-item .nav-desc {
    display: block;
  }
}

/* ── HEIGHT-driven (mobile bottom bar, size) ── */

/* tall enough: stack icon + small label */
@container (min-height: 48px) {
  .nav-item {
    grid-template-areas: "icon" "label";
    grid-template-columns: auto;
    justify-items: center;
  }

  .nav-item .nav-label {
    display: block;
    font-size: 0.6rem;
  }
}

/* ════════ BARS — layout-dependent only ════════ */

.bar-bottom {
  background: #1a1c23;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  padding: 0.4rem 0.25rem;
  height: 50px;
  container-type: size;
}

.bar-bottom .nav-item {
  flex: 1;
}

.bar-side {
  resize: horizontal;
  overflow: hidden;
  width: 200px;
  min-width: 48px;
  max-width: 400px;
  background: #1a1c23;
  border-right: 1px solid rgba(255, 255, 255, 0.08);
  padding: 0.75rem 0.4rem;
  container-type: inline-size;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.hint {
  font-size: 0.55rem;
  color: rgba(255, 255, 255, 0.2);
  text-align: center;
  letter-spacing: 0.05em;
}

@container (max-width: 149px) {
  .hint {
    display: none;
  }
}

/* ════════ Layout shells ════════ */
.mobile-layout {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.desktop-layout {
  display: flex;
  flex-direction: row;
  height: 100%;
}

.content-area {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.15);
  font-size: 0.7rem;
  letter-spacing: 0.05em;
}
</style>