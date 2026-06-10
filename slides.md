---
theme: dracula
title: Container Queries
transition: slide-left
---

# 📦 Container Queries

ビューポートに聞いていたこと、本当はコンテナしか知らない

---
layout: default
---

# 本題に入る前に...

同じナビアイテムを、異なるコンテキストに置いたとき。どうやって対応させますか？

<NavDemo />


---
layout: default
---

# まず思いつく方法は？

```css
/* mobile */
@media (max-width: 768px) {
  .nav-item {
    /* ... */
  }
}

/* desktop */
@media (min-width: 768px) {
  .nav-item {
    /* ... */
  }
}
```

<v-clicks>

- ❌ サイドバーのリサイズには対応できない
- ❌ コンテキストが増えるほど宣言も増える
- ❌ コンポーネントがページ構造に依存する

</v-clicks>

<v-click>

他に方法は？

</v-click>


---
layout: default
---

# JavaScript ならどうか？

<div class="flex items-center gap-2">
  <span>ResizeObserver の出番</span>
  <a href="https://developer.mozilla.org/ja/docs/Web/API/ResizeObserver" target="_blank">
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

- ✅ リサイズ問題はちゃんと解決できる
- ❌ コンポーネントごとに初期化とクリーンアップが必要
- ❌ スタイルのロジックが JS と CSS に分散する

</v-clicks>

---
layout: default
---

# これ、CSS だけでできたら？

> 画面サイズはクエリできるのに、なぜ要素自身のサイズはできない？

<v-click>

実はもうできます。`@container` の登場です！

コンポーネントは自分のコンテナのサイズをクエリして適応できる — JS も、コンテキストセレクタも不要。

```css
@container (min-width: 300px) {
  .nav-item { 
    /* adapt */
  }
}
```

</v-click>

<v-click>

<div class="flex items-center gap-2 my-4">
  <logos-chrome /> <logos-firefox /> <logos-safari /> <logos-microsoft-edge />
  <span class="text-sm opacity-50">Baseline 2023 — グローバルサポート 92%</span>
</div>

</v-click>

<v-click>

<div class="flex items-center gap-2">
  <span>MDN リファレンス</span>
  <a href="https://developer.mozilla.org/ja/docs/Web/CSS/CSS_containment/Container_queries" target="_blank">
    <simple-icons-mdnwebdocs class="size-4 m-1" />
  </a>
</div>

</v-click>

---
layout: default
---

# 仕組み

**コンテナ**とは、基準点として指定した任意の要素のこと。
その**子要素**がコンテナのサイズをクエリして適応できます。

```css
.parent {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .nav-item {
    /* ...  */
  }
}
```

> コンポーネントは自分がどこにいるかを知らない — 使えるスペースの広さだけを知っている。

<div>
<img src="/public/container.png" width="480" class="mx-auto"/>
</div>


---
layout: two-cols-header
---

# `container-type`

::left::

```css
.panel {
  container-type: inline-size;
}
```

- 幅だけをクエリする
- 要素の挙動には影響しない
- 99% はこれを使う

<div class="mt-8">
<ContainerTypeDemo />
</div>

::right::

```css
.panel {
  container-type: size;
}
```

- 幅と高さの両方をクエリする
- 明示的な値がないと高さが潰れる
- 高さのクエリが本当に必要なときだけ

<div class="mt-8">
<ContainerTypeDemo type="size" />
</div>

<style>
.slidev-layout { column-gap: 3rem; }
</style>


---
layout: two-cols-header
---

# コンテナのネスト

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
    /* 最も近いコンテナ = .sidebar */
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

最も近いコンテナが目的のものでないときの逃げ道。

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

名前で参照すれば、最も近いコンテナをスキップできる：

```css
.card {
  @container layout (min-width: 600px) {
    display: grid;
  }
}
```

両方のプロパティは一つのショートハンドにまとめられる：

```css
.layout  { container: layout / inline-size; }
.sidebar { container: sidebar / inline-size; }
```

> 実際には、最も近いコンテナをスキップするケースは稀。命名は可読性の規約として役立つことの方が多い。

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: default
---

# コンテナクエリの単位

| 単位 | 対応するもの |
|---|---|
| <v-mark type="circle" color="#bd93f9">`cqi`</v-mark> | インラインサイズ（LTR では幅） |
| `cqb` | ブロックサイズ（高さ） |
| `cqw` | 物理的な幅 |
| `cqh` | 物理的な高さ |
| `cqmin` | `cqi` / `cqb` の小さい方 |
| `cqmax` | `cqi` / `cqb` の大きい方 |

> `vw`/`vh` と同じ発想 — ただしビューポートではなく、コンテナを基準にする。

---
layout: default
---

# `cqi` の実例


```css
.widget-label { font-size: clamp(0.6rem, 2.5cqi, 0.9rem); }

.widget-value { font-size: clamp(1.2rem, 7cqi, 3rem); } 

.widget-sub { font-size: clamp(0.55rem, 2cqi, 0.8rem); }
```

ブレークポイントなし。フォントがコンテナ幅に合わせてなめらかにスケールする。


<CqiDemo />

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: default
---

# Tailwind v4 も対応済み

CSS だけの機能ではありません — Tailwind v4 なら標準搭載。プラグイン不要です。

```html
<div class="@container">
  <div class="w-full @md:w-1/2 @lg:w-1/3">
    ...
  </div>
</div>
```

<v-click>

`@md` と `@lg` は `md` / `lg` と同じしきい値（`768px` と `1024px`）を使う — ただしビューポートではなく、コンテナ幅を基準に測る。

| クラス | 基準 | しきい値 |
|---|---|---|
| `md:` | ビューポート | ≥ 768px |
| `@md:` | コンテナ | ≥ 768px |

</v-click>

---
layout: two-cols-header
---

# `size` の実践

::left::

<SnapWidgetDemo />

::right::

**両方の軸**が重要なとき、`container-type: size` を使えばコンポーネントが幅*と*高さの両方に応答できる。

- 幅が増える → 右側に地域別の内訳が現れる
- 高さが増える → 下に過去 12 ヶ月の推移が現れる
- `cqmin` / `cqb` が中身をスペースに合わせてスケールする

角をドラッグして、スペースのある場所にコンテンツが現れる様子を見てください。

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: default
---

# まとめ

- **`@media` はページを持つ。`@container` はコンポーネントを持つ。**
- 宣言はコンテキストではなくコンテナに比例する — コンポーネントをどこに置いても、ちゃんと動く
- `inline-size` で 99% のケースをカバー — 設定したら忘れていい
- `size` の出番は両方の軸が重要なとき（リサイズ可能なダッシュボードウィジェット）
- `cqi` で流動的なサイズ調整 — なめらかにスケール、ブレークポイント不要
- ブラウザサポート約 92%、Baseline 2023 — ポリフィル不要

---
layout: two-cols-header
---

# 未来への一歩

<div class="flex items-center gap-2">
  <span>Container scroll-state queries — MDN</span>
  <a href="https://developer.mozilla.org/ja/docs/Web/CSS/Guides/Conditional_rules/Container_scroll-state_queries" target="_blank">
    <simple-icons-mdnwebdocs class="size-4 m-1" />
  </a>
</div>

::left::


stuck・snapped・scrollable の状態を — 純粋な CSS だけで検出する。

```css
.scroller {
  container-type: scroll-state;
}

/* まだ下にコンテンツがある間だけ発火 */
@container scroll-state(scrollable: bottom) {
  .more-cue { opacity: 1; }
}
```

JS のスクロールリスナーを丸ごと置き換える — イベントも `requestAnimationFrame` も、カクつきもなし。

今だと Chromium 系のみ → `@supports` で囲み、プログレッシブエンハンスメントとして扱う。

::right::

<ScrollStateDemo />

<style>
.slidev-layout { column-gap: 3rem; }
</style>

---
layout: center
---

# 次のコンポーネントで試してみてください 📦

<div class="resources">
  <a href="https://developer.mozilla.org/ja/docs/Web/CSS/CSS_containment/Container_queries" target="_blank" class="resource">
    <lucide-link class="inline" /> MDN — Container queries
  </a>
  <a href="https://web.dev/learn/css/container-queries?hl=ja" target="_blank" class="resource">
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