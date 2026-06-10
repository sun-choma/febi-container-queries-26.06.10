<template>
  <div class="demo-root">
    <!-- scroller is the scroll-state container -->
    <div class="scroller">
      <div class="header-wrap">
        <div class="header">
          <span class="dot" />
          <span class="title">Dashboard</span>
          <span class="stuck-tag">stuck!</span>
        </div>
      </div>

      <div class="content">
        <p v-for="n in 12" :key="n" class="line">
          {{ rows[(n - 1) % rows.length] }}
        </p>
      </div>

      <!-- fades out once scrolled to bottom -->
      <div class="more-cue">more below ↓</div>
    </div>
    <div class="hint">↕ scroll inside the box</div>
  </div>
</template>

<script setup>
const rows = [
  'Revenue grew 12.4% this quarter',
  'Active users: 8,230',
  'Avg. session: 3m 42s',
  'Top region: Japan (52%)',
  'Mobile traffic: 68%',
  'Bounce rate down to 42%',
]
</script>

<style scoped>
.demo-root {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.hint {
  font-size: 0.65rem;
  color: rgba(255,255,255,0.35);
  font-family: 'Fira Code', monospace;
  letter-spacing: 0.05em;
}

/* scroller — the scroll-state container */
.scroller {
  container-type: scroll-state;
  position: relative;
  width: 340px;
  height: 280px;
  overflow-y: auto;
  background: #1e1f29;
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 12px;
}

.header-wrap {
  position: sticky;
  top: 0;
  z-index: 2;
}

.header {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  padding: 0.85rem 1rem;
  background: #1e1f29;
  font-family: 'Fira Code', monospace;
  transition: background 0.25s, box-shadow 0.25s;
}

.dot { width: 10px; height: 10px; border-radius: 50%; background: #50fa7b; }
.title { font-weight: 600; color: #f8f8f2; font-size: 0.95rem; flex: 1; }

.stuck-tag {
  font-size: 0.65rem;
  color: #282a36;
  background: #bd93f9;
  padding: 0.15rem 0.5rem;
  border-radius: 99px;
  opacity: 0;
  transform: scale(0.8);
  transition: opacity 0.25s, transform 0.25s;
}

.content { padding: 1rem; }

.line {
  font-family: 'Fira Code', monospace;
  font-size: 0.78rem;
  color: #f8f8f2;
  padding: 0.5rem 0;
  border-bottom: 1px solid rgba(255,255,255,0.05);
}

/* sticky cue pinned to the bottom of the scroll viewport */
.more-cue {
  position: sticky;
  bottom: 0;
  text-align: center;
  font-family: 'Fira Code', monospace;
  font-size: 0.7rem;
  color: #bd93f9;
  padding: 0.5rem;
  background: linear-gradient(transparent, #1e1f29 60%);
  transition: opacity 0.3s;
  pointer-events: none;
}

/* ── React to scroll state ── */
@supports (container-type: scroll-state) {
  /* header gains depth when stuck to the top */
  @container scroll-state(stuck: top) {
    .header {
      background: #282a36;
      box-shadow: 0 4px 16px rgba(0,0,0,0.45);
    }
    .stuck-tag { opacity: 1; transform: scale(1); }
  }

  /* "more below" cue shows only while there's more to scroll down to */
  .more-cue { opacity: 0; }
  @container scroll-state(scrollable: bottom) {
    .more-cue { opacity: 1; }
  }
}
</style>