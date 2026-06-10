<template>
  <div class="demo-root">
    <div class="hint">← ドラッグでリサイズ →</div>
    <div class="resizable">
      <div class="widget">
        <div class="widget-label">月間売上</div>
        <div class="widget-value">¥6,820,000</div>
        <div class="widget-sub">↑ 先月比 12.4%</div>
        <div class="widget-bar">
          <div class="bar-fill" v-for="(h, i) in bars" :key="i" :style="{ height: h + '%' }" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const bars = [40, 55, 35, 70, 60, 85, 75, 90, 65, 80, 95, 72]
</script>

<style scoped>
.demo-root {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 0.4rem;
}

.hint {
  font-size: 0.65rem;
  color: rgba(255,255,255,0.3);
  font-family: 'Fira Code', monospace;
  letter-spacing: 0.05em;
  align-self: center;
}

.resizable {
  resize: horizontal;
  overflow: hidden;
  min-width: 160px;
  max-width: 560px;
  width: 260px;
  container-type: inline-size;
}

.widget {
  background: #1e1f29;
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 12px;
  padding: clamp(0.6rem, 3cqi, 1.5rem);
  display: flex;
  flex-direction: column;
  gap: clamp(0.2rem, 1.5cqi, 0.6rem);
}

.widget-label {
  font-family: 'Fira Code', monospace;
  font-size: clamp(0.6rem, 2.5cqi, 0.9rem);
  color: rgba(255,255,255,0.45);
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.widget-value {
  font-family: 'Fira Code', monospace;
  font-size: clamp(1.2rem, 7cqi, 3rem);
  font-weight: 700;
  color: #f8f8f2;
  line-height: 1;
}

.widget-sub {
  font-family: 'Fira Code', monospace;
  font-size: clamp(0.55rem, 2cqi, 0.8rem);
  color: #50fa7b;
}

.widget-bar {
  display: flex;
  align-items: flex-end;
  gap: clamp(2px, 0.8cqi, 6px);
  height: clamp(32px, 12cqi, 80px);
  margin-top: clamp(0.2rem, 1cqi, 0.5rem);
}

.bar-fill {
  flex: 1;
  background: #bd93f9;
  border-radius: 2px 2px 0 0;
  opacity: 0.7;
  transition: height 0.3s ease;
}
</style>