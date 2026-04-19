# DESIGN.md

> 數據說話，介面退場——讓股票資訊清晰浮現於暗色之上。

## 1. Visual Theme & Atmosphere

**Style**: Dark Minimal — 暗黑科技色板 × 極簡克制裝飾
**Keywords**: 深邃、克制、資料驅動、呼吸感、專業、低噪音、清晰
**Tone**: 冷靜專業、信息優先 — NOT 炫技、花俏、資訊過載
**Feel**: 像夜間飛行艙的儀表板——黑暗中每一個數字都清晰發光

**Interaction Tier**: L2 流暢交互
**Dependencies**: GSAP + ScrollTrigger + IntersectionObserver

---

## 2. Color Palette & Roles

```css
:root {
  /* Backgrounds */
  --bg: #0D0D0F;                          /* 頁面主背景 */
  --surface: #161618;                     /* 卡片/容器 */
  --surface-alt: #1C1C1F;                 /* 交替 section、側欄 */
  --surface-hover: #222226;               /* 卡片 hover 態 */

  /* Borders */
  --border: rgba(255, 255, 255, 0.07);    /* 預設邊框 */
  --border-hover: rgba(255, 255, 255, 0.15); /* 懸停邊框 */

  /* Text */
  --text: #F0F0F2;                        /* 主標題、重要數字 */
  --text-secondary: #9A9AA8;              /* 正文、描述 */
  --text-tertiary: #55555E;               /* 標籤、輔助資訊 */

  /* Accent — 上漲綠 */
  --accent: #22C55E;                      /* 正值、CTA、active 態 */
  --accent-hover: #16A34A;
  --accent-rgb: 34, 197, 94;

  /* Semantic */
  --positive: #22C55E;                    /* 漲 */
  --positive-bg: rgba(34, 197, 94, 0.08);
  --negative: #EF4444;                    /* 跌 */
  --negative-bg: rgba(239, 68, 68, 0.08);
  --neutral: #F59E0B;                     /* 持平/警示 */

  /* RGB helpers */
  --bg-rgb: 13, 13, 15;
  --surface-rgb: 22, 22, 24;

  /* Chart */
  --chart-line: #3B82F6;                  /* 趨勢線 */
  --chart-fill: rgba(59, 130, 246, 0.08); /* 面積填充 */
  --chart-grid: rgba(255, 255, 255, 0.04);/* 網格線 */
}
```

**Color Rules:**
- 所有顏色通過 CSS 變數引用，禁止硬編碼 hex
- 漲跌狀態嚴格用 `--positive` / `--negative`，不能混用其他顏色
- 強調色 `--accent` 每個卡片只出現一次，避免視覺競爭
- 背景層級：`--bg` < `--surface` < `--surface-alt`，不可逆序

---

## 3. Typography Rules

**Font Stack:**
```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Space+Grotesk:wght@500;700&family=JetBrains+Mono:wght@400;500&display=swap');
```

| Role | Font | Size | Weight | Line Height | Letter Spacing |
|------|------|------|--------|-------------|----------------|
| Hero H1 / 股票名 | Space Grotesk | 2.25rem | 700 | 1.2 | -0.02em |
| Section H2 | Space Grotesk | 1.5rem | 700 | 1.3 | -0.01em |
| H3 / 卡片標題 | Inter | 1rem | 600 | 1.4 | — |
| 大數字（股價） | Space Grotesk | 2rem | 700 | 1.1 | -0.03em |
| Body | Inter | 0.9375rem | 400 | 1.6 | — |
| Label / 標籤 | Inter | 0.75rem | 500 | 1.4 | 0.04em |
| Mono / 代碼/股票代碼 | JetBrains Mono | 0.875rem | 500 | 1.5 | 0.02em |

**Typography Rules:**
- 股票代碼（TSLA、GOOGL）一律用 `JetBrains Mono`
- 股價、漲跌幅等數字用 `Space Grotesk` 確保辨識度
- 正文最小 15px，行高 1.6
- **NEVER use**: System UI fallback as primary、Times New Roman、Comic Sans

**Text Decoration:**
- Hero 股票名：無漸變、無投影（克制，數字自己說話）
- 漲跌數字：顏色區分（`--positive` / `--negative`），不加其他裝飾

---

## 4. Component Stylings

### Stock Card
```css
.stock-card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 16px;
  padding: 1.5rem;
  cursor: pointer;
  transition: background 0.2s ease, border-color 0.2s ease, transform 0.2s ease;
}
.stock-card:hover {
  background: var(--surface-hover);
  border-color: var(--border-hover);
  transform: translateY(-2px);
}
.stock-card:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 2px;
}

.stock-card .ticker {
  font-family: 'JetBrains Mono', monospace;
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.04em;
  color: var(--text-tertiary);
  text-transform: uppercase;
}
.stock-card .price {
  font-family: 'Space Grotesk', sans-serif;
  font-size: 1.75rem;
  font-weight: 700;
  color: var(--text);
  letter-spacing: -0.02em;
  margin: 0.25rem 0;
}
.stock-card .change.positive { color: var(--positive); }
.stock-card .change.negative { color: var(--negative); }
```

### Buttons
```css
.btn-primary {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: var(--accent);
  color: #000;
  font-size: 0.875rem;
  font-weight: 600;
  padding: 0.625rem 1.25rem;
  border-radius: 10px;
  border: none;
  cursor: pointer;
  transition: background 0.15s ease, transform 0.1s ease;
}
.btn-primary:hover { background: var(--accent-hover); transform: translateY(-1px); }
.btn-primary:active { transform: translateY(0); }
.btn-primary:focus-visible { outline: 2px solid var(--accent); outline-offset: 3px; }
.btn-primary:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

.btn-ghost {
  background: transparent;
  color: var(--text-secondary);
  border: 1px solid var(--border);
  font-size: 0.875rem;
  font-weight: 500;
  padding: 0.5rem 1rem;
  border-radius: 8px;
  cursor: pointer;
  transition: color 0.15s, border-color 0.15s, background 0.15s;
}
.btn-ghost:hover { color: var(--text); border-color: var(--border-hover); background: var(--surface-hover); }
.btn-ghost:focus-visible { outline: 2px solid var(--accent); outline-offset: 2px; }
.btn-ghost:disabled { opacity: 0.4; cursor: not-allowed; }
```

### Navigation
```css
.nav {
  position: sticky;
  top: 0;
  z-index: 100;
  background: rgba(var(--bg-rgb), 0.85);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border);
  transition: background 0.3s ease, border-color 0.3s ease;
}
.nav.scrolled {
  background: rgba(var(--bg-rgb), 0.95);
  border-color: rgba(255,255,255,0.1);
}
.nav-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 60px;
  padding: 0 2rem;
  max-width: 1280px;
  margin: 0 auto;
}
```

### Tags / Badges
```css
.badge {
  display: inline-flex;
  align-items: center;
  padding: 0.2rem 0.6rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.03em;
}
.badge-positive { background: var(--positive-bg); color: var(--positive); }
.badge-negative { background: var(--negative-bg); color: var(--negative); }
.badge-neutral  { background: rgba(245,158,11,0.1); color: var(--neutral); }

/* Recommendation badge */
.rec-badge {
  padding: 0.375rem 1rem;
  border-radius: 20px;
  font-size: 0.8125rem;
  font-weight: 600;
}
.rec-buy    { background: var(--positive-bg); color: var(--positive); border: 1px solid rgba(34,197,94,0.2); }
.rec-hold   { background: rgba(245,158,11,0.1); color: var(--neutral); border: 1px solid rgba(245,158,11,0.2); }
.rec-sell   { background: var(--negative-bg); color: var(--negative); border: 1px solid rgba(239,68,68,0.2); }
```

### Chart Container
```css
.chart-container {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 16px;
  padding: 1.5rem;
  position: relative;
}
.chart-tabs {
  display: flex;
  gap: 0.25rem;
  margin-bottom: 1rem;
}
.chart-tab {
  padding: 0.3rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 500;
  color: var(--text-tertiary);
  cursor: pointer;
  border: none;
  background: transparent;
  transition: color 0.15s, background 0.15s;
}
.chart-tab.active {
  background: var(--surface-alt);
  color: var(--text);
}
```

### News Card
```css
.news-card {
  padding: 1rem 0;
  border-bottom: 1px solid var(--border);
  display: flex;
  gap: 1rem;
  cursor: pointer;
  transition: opacity 0.15s;
}
.news-card:last-child { border-bottom: none; }
.news-card:hover { opacity: 0.8; }
.news-card .news-meta {
  font-size: 0.75rem;
  color: var(--text-tertiary);
  font-family: 'JetBrains Mono', monospace;
}
.news-card .news-title {
  font-size: 0.9rem;
  font-weight: 500;
  color: var(--text);
  line-height: 1.5;
  margin: 0.25rem 0;
}
```

---

## 5. Layout Principles

**Container:**
- Max width: 1280px
- Padding: 0 2rem (desktop) / 0 1rem (mobile)
- Dashboard grid: 左側清單 320px + 右側主內容 flex: 1

**Spacing Scale:**
- Section padding: 2rem 0
- Component gap: 1.5rem
- Card internal padding: 1.5rem
- 小元件間距: 0.75rem

**Grid:**
```css
/* 主布局：側欄 + 主內容 */
.dashboard-layout {
  display: grid;
  grid-template-columns: 320px 1fr;
  grid-template-rows: auto 1fr;
  gap: 1.5rem;
  min-height: calc(100vh - 60px);
}

/* 右側主區：圖表 + 分析 + 新聞 */
.main-grid {
  display: grid;
  grid-template-columns: 1fr 360px;
  gap: 1.5rem;
}

/* 股票清單卡片 */
.stock-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}
```

---

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat | border only | 頁面背景區塊 |
| Surface | `box-shadow: 0 1px 3px rgba(0,0,0,0.3)` | 一般卡片 |
| Elevated | `box-shadow: 0 4px 16px rgba(0,0,0,0.4)` | hover 態卡片、Modal |
| Glow | `box-shadow: 0 0 0 1px var(--accent), 0 4px 20px rgba(var(--accent-rgb),0.15)` | 選中股票、Focus |

---

## 7. Animation & Interaction

**Motion Philosophy**: 克制目的性動效——只用 opacity 和 transform，數據變化用計數器動畫強調

**Tier**: L2

### Dependencies
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
```

### Base Setup
```js
gsap.registerPlugin(ScrollTrigger);

// 數字計數器動畫
function animateNumber(el, from, to, decimals = 2) {
  gsap.fromTo({ val: from }, { val: to }, {
    duration: 1.2,
    ease: 'power2.out',
    onUpdate: function() { el.textContent = this.targets()[0].val.toFixed(decimals); }
  });
}
```

### Entrance Animation
```css
.fade-up {
  opacity: 0;
  transform: translateY(16px);
}
.fade-up.visible {
  animation: fadeUp 0.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}
@keyframes fadeUp {
  to { opacity: 1; transform: translateY(0); }
}
```

### Scroll Behavior
```js
// 卡片滾動入場（stagger）
gsap.utils.toArray('.stock-card').forEach((card, i) => {
  gsap.from(card, {
    scrollTrigger: { trigger: card, start: 'top 88%' },
    opacity: 0,
    y: 20,
    duration: 0.5,
    delay: i * 0.08,
    ease: 'power2.out'
  });
});

// 新聞 section 入場
gsap.from('.news-section', {
  scrollTrigger: { trigger: '.news-section', start: 'top 80%' },
  opacity: 0, y: 24, duration: 0.6, ease: 'power2.out'
});
```

### Hover & Focus States
```css
/* 卡片：translateY(-2px) 已定義在 Component */
/* 導航 active 指示線 */
.nav-link {
  position: relative;
  color: var(--text-secondary);
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  padding: 0.25rem 0;
  transition: color 0.15s;
}
.nav-link::after {
  content: '';
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 0;
  height: 2px;
  background: var(--accent);
  border-radius: 1px;
  transition: width 0.2s ease;
}
.nav-link:hover, .nav-link.active { color: var(--text); }
.nav-link.active::after { width: 100%; }
```

### Special Effects
```js
// 股價更新時閃爍效果
function flashPrice(el, direction) {
  const color = direction > 0 ? 'var(--positive)' : 'var(--negative)';
  gsap.fromTo(el, { color }, { color: 'var(--text)', duration: 1.5, ease: 'power1.out' });
}

// 導航滾動狀態
window.addEventListener('scroll', () => {
  document.querySelector('.nav').classList.toggle('scrolled', window.scrollY > 20);
}, { passive: true });
```

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
  .fade-up { opacity: 1; transform: none; }
}
```

---

## 8. Do's and Don'ts

### Do
- 用顏色（綠/紅）和數字大小傳達漲跌，讓資訊自己說話
- 卡片選中態用 glow 邊框，清晰標示當前焦點
- 圖表區域給足空間（佔右側主區 2/3 高度）
- 新聞條目精簡：來源、時間、標題三行足矣
- 所有數字動畫用 ease-out，避免反彈感

### Don't
- ❌ 不在卡片背景加漸變——純色 surface 更克制
- ❌ 不使用超過三種強調色——只有 positive/negative/accent
- ❌ 不在同一視窗顯示超過 8 條新聞，超過則分頁
- ❌ 不用 `filter: blur()` 在運動元素上（損性能）
- ❌ 不讓圖表 loading 時閃白——用骨架屏（skeleton）佔位
- ❌ 不添加音效或自動播放任何媒體
- ❌ 不用純色塊佔位圖示，用內聯 SVG icon
- ❌ 不在 mobile 顯示側欄和主內容並排——切換為 tab 結構
- ❌ 不在技術指標區展示超過 6 個指標——精選最重要的
- ❌ 不讓推薦標籤（買入/持有/賣出）無依據——需附簡短理由

---

## 9. Responsive Behavior

**Breakpoints:**
| Name | Width | Key Changes |
|------|-------|-------------|
| Desktop | > 1024px | 側欄 + 主內容並排，完整圖表 |
| Tablet | 768px–1024px | 側欄折疊為頂部橫向清單 |
| Mobile | < 768px | 單欄，Tab 切換清單/圖表/新聞 |

**Touch Targets:** minimum 44×44px
**Collapsing Strategy:** 側欄優先折疊；圖表高度從 360px 降至 240px；技術指標 grid 2 欄→ 1 欄

```css
@media (max-width: 1024px) {
  .dashboard-layout {
    grid-template-columns: 1fr;
  }
  .stock-list {
    flex-direction: row;
    overflow-x: auto;
    gap: 0.75rem;
    padding-bottom: 0.5rem;
    scroll-snap-type: x mandatory;
  }
  .stock-card { min-width: 200px; scroll-snap-align: start; }
}

@media (max-width: 768px) {
  .main-grid {
    grid-template-columns: 1fr;
  }
  .nav-inner { padding: 0 1rem; }
  .dashboard-layout { padding: 0 1rem; }

  /* Tab 切換 */
  .mobile-tabs {
    display: flex;
    border-bottom: 1px solid var(--border);
    margin-bottom: 1rem;
  }
  .mobile-tab {
    flex: 1;
    padding: 0.75rem;
    text-align: center;
    font-size: 0.875rem;
    font-weight: 500;
    color: var(--text-tertiary);
    border: none;
    background: transparent;
    cursor: pointer;
    transition: color 0.15s;
  }
  .mobile-tab.active { color: var(--accent); border-bottom: 2px solid var(--accent); }
}
```
