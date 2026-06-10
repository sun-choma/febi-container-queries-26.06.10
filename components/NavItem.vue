<template>
  <div class="nav-item" :class="{ active }">
    <span class="nav-icon"><component :is="icon" :size="18"/></span>
    <div class="nav-text">
      <div class="nav-label">{{ label }}</div>
      <div class="nav-desc">{{ desc }}</div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  icon: {type: [Object, Function], required: true},
  label: {type: String, required: true},
  desc: {type: String, default: ''},
  active: {type: Boolean, default: false},
})
</script>

<style scoped>
/* Layout is STATIC: icon pinned left, text flows to its right.
   The item clips its own overflow — so when the container is narrow,
   the text is simply masked out. Widening the container reveals it.
   No display toggling, no layout jump. */
.nav-item {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  padding: 0.3rem;
  border-radius: 7px;
  cursor: default;
  overflow: hidden;
}

.nav-item.active {
  background: rgba(189, 147, 249, 0.15);
}

/* icon never moves or shrinks */
.nav-icon {
  flex: 0 0 auto;
  width: 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #f8f8f2;
}

.nav-item.active .nav-icon {
  color: #bd93f9;
}

/* text block sits beside the icon and gets clipped by the item */
.nav-text {
  flex: 1 1 auto;
  min-width: 0;
}

.nav-label {
  font-size: 0.8rem;
  color: #f8f8f2;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.nav-item.active .nav-label {
  color: #bd93f9;
}

.nav-desc {
  font-size: 0.65rem;
  color: rgba(255, 255, 255, 0.4);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: none;
}

/* Wide enough → reveal the subtitle */
@container (min-width: 200px) {
  .nav-desc {
    display: block;
  }
}
</style>