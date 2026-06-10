<template>
  <div class="demo-wrap">
    <div class="container-box" :style="{ containerType: type }">
      <div class="card">
        <div class="card-title">Browser Usage 2025</div>
        <div class="card-body">
          <div class="stat" v-for="b in browsers" :key="b.name">
            <span class="icon"><component :is="b.icon" /></span>
            <span class="name">{{ b.name }}</span>
            <span class="bar-wrap"><span class="bar" :style="{ width: b.pct + '%' }" /></span>
            <span class="pct">{{ b.pct }}%</span>
          </div>
        </div>
      </div>
    </div>
    <div class="label">container-type: {{ type }}</div>
  </div>
</template>

<script setup>
import { defineComponent, h } from 'vue'
import { Icon } from '@iconify/vue'

defineProps({
  type: {
    type: String,
    default: 'inline-size'
  }
})

const iconComp = (name) => defineComponent({ render: () => h(Icon, { icon: name, width: '14', height: '14' }) })

const browsers = [
  { name: 'Chrome',  icon: iconComp('logos:chrome'),          pct: 65.2 },
  { name: 'Safari',  icon: iconComp('logos:safari'),          pct: 18.4 },
  { name: 'Edge',    icon: iconComp('logos:microsoft-edge'),  pct:  4.8 },
  { name: 'Firefox', icon: iconComp('logos:firefox'),         pct:  4.1 },
  { name: 'Samsung', icon: iconComp('logos:samsung'),         pct:  2.7 },
  { name: 'Other',   icon: iconComp('logos:html-5'),          pct:  4.8 },
]
</script>

<style scoped>
.demo-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.label {
  font-size: 0.65rem;
  font-family: 'Fira Code', monospace;
  color: rgba(255, 255, 255, 0.4);
  letter-spacing: 0.05em;
}

.container-box {
  width: 200px;
  min-height: 110px;
  background: #1e1f29;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  overflow: hidden;
}

.card {
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}

.card-title {
  font-size: 0.75rem;
  font-weight: 600;
  color: #bd93f9;
  font-family: 'Fira Code', monospace;
}

.card-body {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
}

.stat {
  display: grid;
  grid-template-columns: 16px 60px 1fr 36px;
  align-items: center;
  gap: 0.35rem;
  font-size: 0.68rem;
  font-family: 'Fira Code', monospace;
  color: #f8f8f2;
}

.icon {
  display: flex;
  align-items: center;
}

.name {
  opacity: 0.8;
}

.bar-wrap {
  background: rgba(255,255,255,0.08);
  border-radius: 2px;
  height: 4px;
  overflow: hidden;
}

.bar {
  display: block;
  height: 100%;
  background: #bd93f9;
  border-radius: 2px;
}

.pct {
  text-align: right;
  opacity: 0.6;
}
</style>