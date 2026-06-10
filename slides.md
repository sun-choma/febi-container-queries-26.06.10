---
theme: dracula
title: Container Queries
transition: slide-left
---

# 📦 Container Queries

Stop asking the viewport what only the container knows

---
layout: default
---

# Before we dive in...

Here's a nav item living in different contexts. How would you style it to adapt?

<NavDemo />


---
layout: default
---

# The obvious solution?

```css
/* mobile */
@media (max-width: 768px) {
  .nav-item { /* bottom nav styles */
  }
}

/* desktop */
@media (min-width: 769px) {
  .nav-item { /* sidebar styles */
  }
}
```

<v-clicks>

- ❌ Doesn't solve sidebar resize
- ❌ More contexts = more declarations
- ❌ Component depends on page structure

</v-clicks>

<v-click>

Any alternatives?

</v-click>


---
layout: default
---

# What about JavaScript?

<div class="flex items-center gap-2">
  <span>ResizeObserver to the resque</span>
  <a href="https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver" target="_blank">
    <simple-icons-mdnwebdocs class="size-4 m-1" />
  </a>
</div>

```tsx
const [size, setSize] = useState('expanded')

useEffect(() => {
    const observer = new ResizeObserver(([entry]) => {
        const width = entry.contentRect.width
        if (width < 56) setSize('collapsed')
        else if (width < 260) setSize('expanded')
        else setSize('wide')
    })

    observer.observe(navRef.current!)
    return () => observer.disconnect()
}, [])

return
...
```

<v-clicks>

- ✅ Actually solves the resize problem
- ❌ Init and cleanup on every component instance
- ❌ Styling logic split across JS and CSS

</v-clicks>

---
layout: default
---

# What if CSS could do this natively?

> We can query the screen size to adjust layout — why not the element itself?

<v-click>

We already can. Meet `@container`!

A component can query its own container's size and adapt — no JS, no context selectors.

```css
@container (min-width: 300px) {
  .nav-item { /* adapt */
  }
}
```

</v-click>

<v-click>

<div class="flex items-center gap-2 my-4">
  <logos-chrome /> <logos-firefox /> <logos-safari /> <logos-microsoft-edge />
  <span class="text-sm opacity-50">Baseline 2023 — 92% global support</span>
</div>

</v-click>

<v-click>

<div class="flex items-center gap-2">
  <span>MDN Reference</span>
  <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries" target="_blank">
    <simple-icons-mdnwebdocs class="size-4 m-1" />
  </a>
</div>

</v-click>

---
layout: default
---

# How it works

A **container** is any element you designate as a reference point.
Its **children** can then query its size and adapt.

```css
.sidebar {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .nav-item {
    /* Adaptive styles here  */
  }
}
```

> The component doesn't know where it lives — only how much space it has.

<div>
<img src="/public/container.png" width="480" class="mx-auto"/>
</div>


---
layout: two-cols-header
---

# `container-type`

::left::

```css
.sidebar {
  container-type: inline-size;
}
```

- Queries width only
- Doesn't affect element behaviour
- Use this 99% of the time

<div class="mt-8">
<ContainerTypeDemo />
</div>

::right::

```css
.panel {
  container-type: size;
}
```

- Queries both width and height
- Height collapses without explicit value
- Only when height queries actually matter

<div class="mt-8">
<ContainerTypeDemo type="size" />
</div>

<style>
.slidev-layout { column-gap: 3rem; }
</style>


---
layout: two-cols-header
---

# Nesting containers

::left::

```html
<div class="layout">      <!-- container -->
  <div class="sidebar">   <!-- container -->
    <div class="nav-item"> ... </div>
  </div>
</div>
```

```css
.layout  { container-type: inline-size; }
.sidebar { container-type: inline-size; }

.nav-item {
  @container (min-width: 200px) {
    /* nearest container = .sidebar */
    display: flex;
  }
}
```

::right::

```html
<div class="sidebar">   <!-- container -->
  <div class="card">
    <h2 class="card-title">...</h2>
  </div>
</div>
```

```css
.sidebar { container-type: inline-size; }

.card {
  display: block;

  @container (min-width: 300px) {
    display: grid;
  }
}

.card-title {
  font-size: 0.9rem;

  @container (min-width: 300px) {
    font-size: 1.2rem;
  }
}
```

<style>
.slidev-layout { column-gap: 3rem; }
</style>


---
layout: two-cols-header
---

# `container-name`

Escape hatch for when nearest isn't the right container.

::left::

```html
<main class="layout">
  <aside class="sidebar">
    <div class="card">...</div>
  </aside>
</main>
```

```css
.layout  { 
  container-name: layout;
  container-type: inline-size;
}
.sidebar { 
  container-name: sidebar;
  container-type: inline-size;
}
```

::right::

Then reference by name to skip the nearest container:

```css
.card {
  @container layout (min-width: 600px) {
    display: grid;
  }
}
```

Both properties can be combined into a single shorthand:

```css
.layout  { container: layout / inline-size; }
.sidebar { container: sidebar / inline-size; }
```

> In practice, skipping the nearest container is rare. Naming is more useful as a readability convention.

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: default
---

# Container Query Units

| Unit | Resolves to |
|---|---|
| <v-mark type="circle" color="#bd93f9">`cqi`</v-mark> | inline size (width in LTR) |
| `cqb` | block size (height) |
| `cqw` | physical width |
| `cqh` | physical height |
| `cqmin` | smaller of `cqi` / `cqb` |
| `cqmax` | larger of `cqi` / `cqb` |

> Same idea as `vw`/`vh` — but scoped to the container, not the viewport.

---
layout: default
---

# `cqi` in action


```css
.widget-label { font-size: clamp(0.6rem, 2.5cqi, 0.9rem); }

.widget-value { font-size: clamp(1.2rem, 7cqi, 3rem); } 

.widget-sub { font-size: clamp(0.55rem, 2cqi, 0.8rem); }
```

No breakpoints. Font scales smoothly with container width.


<CqiDemo />

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: default
---

# Tailwind v4 has it covered

Not a CSS-only feature — if you're on Tailwind v4, it's built in. No plugins needed.

```html
<div class="@container">
  <div class="w-full @md:w-1/2 @lg:w-1/3">
    ...
  </div>
</div>
```

<v-click>

`@md` and `@lg` use the same thresholds as `md` and `lg` — `768px` and `1024px` — but measured against the container width, not the viewport.

| Class | Reference | Threshold |
|---|---|---|
| `md:` | viewport | ≥ 768px |
| `@md:` | container | ≥ 768px |

</v-click>

---
layout: two-cols-header
---

# `size` in practice

::left::

<SnapWidgetDemo />

::right::

When **both axes** matter, `container-type: size` lets the component respond to width *and* height.

- Extra width → reveals regional breakdown on the right
- Extra height → reveals the 12-month trend below
- `cqmin` / `cqb` scale the contents to fit

Drag the corner and watch content appear where there's room for it.

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: default
---

# Takeaways

- **`@media` owns the page. `@container` owns the component.**
- Declarations scale with containers, not contexts — drop a component anywhere, it just works
- `inline-size` covers 99% of cases — set it and forget it
- `size` earns its place when both axes matter (resizable dashboard widgets)
- `cqi` for fluid sizing — smooth scaling, no breakpoints
- ~92% browser support, Baseline 2023 — no polyfill needed

---
layout: two-cols-header
---

# A glimpse into the future

<div class="flex items-center gap-2">
  <span>Container scroll-state queries — MDN</span>
  <a href="https://developer.mozilla.org/ja/docs/Web/CSS/Guides/Conditional_rules/Container_scroll-state_queries" target="_blank">
    <simple-icons-mdnwebdocs class="size-4 m-1" />
  </a>
</div>

::left::

### Scroll-state queries

Detect stuck, snapped, and scrollable states — in pure CSS.

```css
.scroller {
  container-type: scroll-state;
}

/* fires while more content lies below */
@container scroll-state(scrollable: bottom) {
  .more-cue { opacity: 1; }
}
```

Replaces JS scroll listeners entirely — no events, no `requestAnimationFrame`, no jank.

Chrome only for now → wrap in `@supports`, treat as progressive enhancement.

::right::

<ScrollStateDemo />

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: center
---

# Try it in your next component 📦

<div class="resources">
  <a href="https://developer.mozilla.org/ja/docs/Web/CSS/CSS_containment/Container_queries" target="_blank" class="resource">
    <lucide-link class="inline" /> MDN — Container queries
  </a>
  <a href="https://web.dev/learn/css/container-queries?hl=en" target="_blank" class="resource">
    <lucide-link class="inline" /> web.dev — Container Queries
  </a>
  <a href="https://caniuse.com/css-container-queries" target="_blank" class="resource">
    <lucide-link class="inline" /> Can I use — Container Queries
  </a>
</div>

<style scoped>
.resources {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
  margin-top: 2rem;
  font-size: 0.875rem;
}
.resource {
  padding: 0.4rem 0.875rem;
  border-radius: 0.375rem;
  background: #282a36;
  border: 1px solid #44475a;
  text-decoration: none;
  color: #f8f8f2;
  transition: border-color 200ms ease;
}
.resource:hover {
  border-color: #bd93f9;
}
</style>