<template>
  <div class="demo-root">
    <div class="hint">↘ 角をドラッグ — グリッドにスナップ</div>

    <div class="grid-stage" ref="stage">
      <!-- background grid as absolute overlay -->
      <div class="grid-bg">
        <div class="grid-cell" v-for="n in 9" :key="n" />
      </div>

      <!-- the resizable widget -->
      <div
          class="widget"
          :style="{
          gridColumn: `1 / span ${cols}`,
          gridRow: `1 / span ${rows}`,
        }"
      >
        <div class="widget-inner">
          <!-- core: always visible -->
          <div class="core">
            <div class="label">売上</div>
            <div class="value">¥6.82M</div>
            <div class="delta">↑ 12.4%</div>
          </div>

          <!-- right panel: revealed with extra horizontal space -->
          <div class="right-panel">
            <div class="panel-title">地域別</div>
            <div class="region"><span>🇯🇵 日本</span><span>52%</span></div>
            <div class="region"><span>🇺🇸 米国</span><span>28%</span></div>
            <div class="region"><span>🇩🇪 ドイツ</span><span>11%</span></div>
            <div class="region"><span>🌍 その他</span><span>9%</span></div>
          </div>

          <!-- bottom panel: revealed with extra vertical space -->
          <div class="bottom-panel">
            <div class="panel-title">過去 12 ヶ月</div>
            <div class="spark">
              <div class="bar" v-for="(h, i) in bars" :key="i" :style="{ height: h + '%' }" />
            </div>
          </div>
        </div>
      </div>

      <!-- drag handle -->
      <div
          class="handle"
          :style="{
          left: `calc(${(cols / 3) * 100}% - 9px)`,
          top: `calc(${(rows / 3) * 100}% - 9px)`,
        }"
          @pointerdown="startDrag"
      />
    </div>

    <div class="size-label">{{ cols }} × {{ rows }}</div>
  </div>
</template>
<script setup>
import { ref } from 'vue'

const stage = ref(null)
const cols = ref(2)
const rows = ref(2)

const bars = [40, 55, 35, 70, 60, 85, 75, 90, 65, 80, 95, 72]

let dragging = false

function startDrag(e) {
  dragging = true
  e.target.setPointerCapture(e.pointerId)
  window.addEventListener('pointermove', onMove)
  window.addEventListener('pointerup', stop)
}

function onMove(e) {
  if (!dragging || !stage.value) return
  const rect = stage.value.getBoundingClientRect()
  const x = (e.clientX - rect.left) / rect.width
  const y = (e.clientY - rect.top) / rect.height
  cols.value = Math.min(3, Math.max(1, Math.ceil(x * 3)))
  rows.value = Math.min(3, Math.max(1, Math.ceil(y * 3)))
}

function stop() {
  dragging = false
  window.removeEventListener('pointermove', onMove)
  window.removeEventListener('pointerup', stop)
}
</script>

<style scoped>
.demo-root {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.hint, .size-label {
  font-size: 0.65rem;
  color: rgba(255,255,255,0.35);
  font-family: 'Fira Code', monospace;
  letter-spacing: 0.05em;
}

.grid-stage {
  position: relative;
  width: 360px;
  height: 360px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
  gap: 8px;
}

.grid-bg {
  position: absolute;
  inset: 0;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
  gap: 8px;
  pointer-events: none;
  z-index: 0;
}

.grid-cell {
  background: rgba(255,255,255,0.03);
  border: 1px solid rgba(255,255,255,0.06);
  border-radius: 8px;
}

/* ── Widget ── */
.widget {
  position: relative;
  z-index: 1;
  background: #1e1f29;
  border: 1px solid rgba(189,147,249,0.4);
  border-radius: 12px;
  overflow: hidden;
  container-type: size;
}

.widget-inner {
  width: 100%;
  height: 100%;
  padding: clamp(0.5rem, 5cqmin, 1.25rem);
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: auto;
  grid-template-areas: "core";
  gap: clamp(0.4rem, 3cqmin, 1rem);
}

.core {
  grid-area: core;
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: clamp(0.1rem, 1cqmin, 0.4rem);
  min-width: 0;
}

.label {
  font-family: 'Fira Code', monospace;
  font-size: clamp(0.55rem, 4cqmin, 0.9rem);
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: rgba(255,255,255,0.45);
}

.value {
  font-family: 'Fira Code', monospace;
  font-size: clamp(1.1rem, 12cqmin, 2.6rem);
  font-weight: 700;
  color: #f8f8f2;
  line-height: 1;
}

.delta {
  font-family: 'Fira Code', monospace;
  font-size: clamp(0.5rem, 3.5cqmin, 0.85rem);
  color: #50fa7b;
}

/* hidden by default */
.right-panel, .bottom-panel {
  display: none;
  min-width: 0;
}

.panel-title {
  font-family: 'Fira Code', monospace;
  font-size: clamp(0.5rem, 3cqmin, 0.75rem);
  text-transform: uppercase;
  letter-spacing: 0.06em;
  color: rgba(255,255,255,0.4);
  margin-bottom: 0.4rem;
}

.region {
  display: flex;
  justify-content: space-between;
  font-family: 'Fira Code', monospace;
  font-size: clamp(0.55rem, 3cqmin, 0.8rem);
  color: #f8f8f2;
  opacity: 0.85;
  padding: 0.1rem 0;
}

.spark {
  display: flex;
  align-items: flex-end;
  gap: clamp(2px, 1cqmin, 5px);
  height: clamp(28px, 22cqb, 70px);
}

.bar {
  flex: 1;
  background: #bd93f9;
  border-radius: 2px 2px 0 0;
  opacity: 0.7;
}

/* ── Reveal on extra horizontal space ── */
@container (min-width: 230px) {
  .widget-inner {
    grid-template-columns: 1fr 1fr;
    grid-template-areas: "core right";
  }
  .right-panel {
    grid-area: right;
    display: block;
    padding-left: clamp(0.4rem, 3cqmin, 1rem);
  }
}

/* ── Reveal on extra vertical space ── */
@container (min-height: 200px) {
  .widget-inner {
    grid-template-rows: 1fr auto;
    grid-template-areas: "core" "bottom";
  }
  .bottom-panel {
    grid-area: bottom;
    display: block;
    padding-top: clamp(0.4rem, 3cqmin, 0.8rem);
  }
}

/* ── Both axes large: full 2x2 content layout ── */
@container (min-width: 230px) and (min-height: 230px) {
  .widget-inner {
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1fr auto;
    grid-template-areas:
      "core  right"
      "bottom bottom";
  }
}

/* ── Handle ── */
.handle {
  position: absolute;
  width: 18px;
  height: 18px;
  background: #bd93f9;
  border: 2px solid #282a36;
  border-radius: 50%;
  cursor: nwse-resize;
  z-index: 10;
  touch-action: none;
  transition: transform 0.1s;
}
.handle:hover {
  transform: scale(1.2);
}
</style>