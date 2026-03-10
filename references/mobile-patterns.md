# Mobile Design Patterns — Comprehensive Reference

## Safe Area Management

### The Problem
Modern mobile devices have areas that overlap content:
- **Status bar** (top) — clock, battery, signal
- **Navigation bar** (bottom) — back, home, recent buttons or gesture bar
- **Notch / Dynamic Island** (top center on some devices)
- **Rounded corners** — content clipped in corners on some devices

### Platform-Specific Solutions

#### Android + Capacitor (WebView)
Android WebView has limited CSS support. Key limitations:
- `env(safe-area-inset-*)` — NOT reliably supported
- `max()` CSS function — NOT reliably supported
- `viewport-fit=cover` — supported but env() fallback fails

**Recommended approach:**
```css
/* Simple fixed padding — works everywhere */
#root {
  padding-top: 36px;    /* typical Android status bar */
  padding-bottom: 20px; /* gesture bar clearance */
}
```

**Advanced approach — inject from native:**
```java
// In MainActivity.java onStart()
int statusBarHeight = getResources().getDimensionPixelSize(
  getResources().getIdentifier("status_bar_height", "dimen", "android")
);
float density = getResources().getDisplayMetrics().density;
int statusDp = Math.round(statusBarHeight / density);

// Inject as CSS variable
String js = "document.documentElement.style.setProperty('--status-bar-h', '" + statusDp + "px');";
getBridge().getWebView().evaluateJavascript(js, null);
```

Then in CSS:
```css
#root {
  padding-top: var(--status-bar-h, 36px);
  padding-bottom: var(--nav-bar-h, 20px);
}
```

#### iOS + Capacitor
iOS Safari WebView supports `env()`:
```css
#root {
  padding-top: env(safe-area-inset-top, 20px);
  padding-bottom: env(safe-area-inset-bottom, 0px);
}
```

### Full-Screen Overlays
When a component uses `position: fixed; inset: 0`, it escapes parent padding:
```jsx
<div className="fixed inset-0 z-50" style={{ paddingTop: 36, paddingBottom: 20 }}>
  {/* Content here is safe from system UI */}
</div>
```

## Navigation Patterns

### Bottom Tab Bar
Classic iOS/Android pattern. 3-5 tabs maximum.

```jsx
<nav className="shrink-0 flex border-t border-surface-2 bg-bg/90 backdrop-blur-md">
  {tabs.map(tab => (
    <button
      key={tab.key}
      onClick={() => setTab(tab.key)}
      className={`flex-1 flex flex-col items-center py-2 gap-0.5 ${
        active === tab.key ? 'text-primary-light' : 'text-text-dim/60'
      }`}
    >
      <tab.icon size={20} />
      <span className="text-[10px] font-medium">{tab.label}</span>
    </button>
  ))}
</nav>
```

**Critical:** Must account for bottom safe area:
```css
/* Add bottom padding to tab bar container, not the bar itself */
nav { padding-bottom: env(safe-area-inset-bottom, 20px); }
```

### Top Pill Navigation
Alternative to bottom tabs, avoids bottom safe-area issues:

```jsx
<div className="flex gap-1.5 px-4 pb-2">
  {tabs.map(tab => (
    <button
      key={tab.key}
      onClick={() => setTab(tab.key)}
      className={`px-3.5 py-1.5 rounded-full text-xs font-medium transition-colors ${
        active === tab.key
          ? 'bg-primary-light text-white'
          : 'bg-surface-2 text-text-dim'
      }`}
    >
      {tab.label}
    </button>
  ))}
</div>
```

### Drawer / Side Menu
For apps with many sections. Use `transform: translateX(-100%)` for hidden state.

### Header + Back Navigation
For detail views and nested navigation:
```jsx
<header className="shrink-0 flex items-center gap-3 px-4 py-3">
  <button onClick={onBack} className="w-10 h-10 flex items-center justify-center -ml-2">
    <IoChevronBack size={24} />
  </button>
  <h1 className="text-base font-bold truncate flex-1">{title}</h1>
</header>
```

## App Shell Pattern

The standard mobile app layout:

```jsx
<div className="h-full flex flex-col bg-bg">
  {/* Fixed header — never scrolls */}
  <header className="shrink-0 px-4 pt-1">
    {/* Logo, title, tabs */}
  </header>

  {/* Scrollable content — takes remaining space */}
  <main className="flex-1 min-h-0 overflow-y-auto">
    {/* Page content */}
  </main>

  {/* Fixed footer — mini player, tab bar, etc */}
  <footer className="shrink-0">
    {/* Bottom UI */}
  </footer>
</div>
```

Key CSS:
- `h-full` on root — fills viewport
- `flex flex-col` — vertical stack
- `shrink-0` on header/footer — never compress
- `flex-1 min-h-0` on content — takes all remaining space AND allows scrolling (min-h-0 is critical!)

## Card Design Patterns

### Track / List Item Card
```jsx
<div className="flex items-center gap-3 py-2.5 px-4 active:bg-surface-2/50">
  <div className="w-11 h-11 rounded-lg overflow-hidden shrink-0 bg-surface-3">
    {/* Image or icon fallback */}
  </div>
  <div className="flex-1 min-w-0">
    <p className="text-[13px] font-medium truncate">{title}</p>
    <p className="text-[11px] text-text-dim truncate mt-0.5">{subtitle}</p>
  </div>
  <span className="text-[11px] text-text-dim tabular-nums shrink-0">{meta}</span>
</div>
```

### Album / Grid Card
```jsx
<button className="w-[110px] text-left shrink-0">
  <div className="w-[110px] h-[110px] rounded-xl overflow-hidden bg-surface-3 mb-1.5">
    {/* Cover image */}
  </div>
  <p className="text-[11px] font-medium truncate leading-tight">{title}</p>
  <p className="text-[10px] text-text-dim truncate leading-tight">{subtitle}</p>
</button>
```

### Stat Card
```jsx
<div className="bg-surface-2 rounded-2xl p-3.5 text-center">
  <p className="text-xl font-bold" style={{ color }}>{value}</p>
  <p className="text-[10px] text-text-dim mt-0.5">{label}</p>
</div>
```

## Horizontal Scroll Pattern

For artists, albums, playlists — edge-to-edge scrolling:

```jsx
<div className="flex gap-3 overflow-x-auto pb-1 -mx-4 px-4">
  {items.map(item => (
    <div key={item.id} className="shrink-0 w-[68px]">
      {/* Card content */}
    </div>
  ))}
</div>
```

- `-mx-4 px-4` — extends scroll area to screen edges while keeping content padded
- `shrink-0` on children — prevents flex from squishing items
- `pb-1` — space for subtle scrollbar on desktop
- Hide scrollbar: `::-webkit-scrollbar { width: 0; height: 0; }`

## Mini Player Pattern

Persistent at bottom, shows current track. Tappable to expand to full-screen Now Playing.

```jsx
<div className="shrink-0 mx-3 mb-2 rounded-2xl overflow-hidden bg-surface-2 border border-surface-3/40">
  {/* Progress bar */}
  <div className="h-[2px] bg-surface-3">
    <div className="h-full bg-primary-light" style={{ width: `${progress}%` }} />
  </div>

  <button onClick={openNowPlaying} className="w-full flex items-center gap-2.5 px-3 py-2">
    {/* Cover + Info + Controls */}
  </button>
</div>
```

## Full-Screen Now Playing Pattern

Overlay that slides up from bottom:

```jsx
<div className="fixed inset-0 z-50 bg-bg animate-slide-up flex flex-col"
     style={{ paddingTop: 36, paddingBottom: 20 }}>
  {/* Blurred cover background */}
  <div className="absolute inset-0 overflow-hidden pointer-events-none">
    <img src={coverUrl} className="w-full h-full object-cover blur-[60px] opacity-20 scale-150" />
  </div>

  {/* Top bar — close + title + favorite */}
  <div className="relative shrink-0 flex items-center justify-between px-4 pt-3 pb-1">
    {/* ... */}
  </div>

  {/* Centered content — album art + info + controls */}
  <div className="relative flex-1 flex flex-col items-center justify-center px-10 min-h-0">
    {/* ... */}
  </div>

  {/* Bottom controls */}
  <div className="relative shrink-0 px-10 pb-4">
    {/* Play/pause, next, prev, shuffle, repeat, volume */}
  </div>
</div>
```

CSS animation:
```css
@keyframes slide-up {
  from { transform: translateY(100%); }
  to { transform: translateY(0); }
}
.animate-slide-up {
  animation: slide-up 0.25s ease-out;
}
```

## Search Pattern

Input with icon and clear button:
```jsx
<div className="relative">
  <IoSearch className="absolute left-3 top-1/2 -translate-y-1/2 text-text-dim" size={16} />
  <input
    type="text"
    placeholder="Sök..."
    className="w-full pl-9 pr-9 py-2.5 rounded-xl bg-surface-2 text-sm text-text
               placeholder:text-text-dim/60 focus:outline-none focus:ring-1 focus:ring-primary-light/40"
  />
  {query && (
    <button onClick={clear} className="absolute right-3 top-1/2 -translate-y-1/2">
      <IoClose size={16} className="text-text-dim" />
    </button>
  )}
</div>
```

## Loading States

### Skeleton Screen
Better than spinners — shows the shape of content that's loading:
```jsx
<div className="animate-pulse">
  <div className="w-11 h-11 rounded-lg bg-surface-3" />
  <div className="flex-1 space-y-2">
    <div className="h-3 w-3/4 rounded bg-surface-3" />
    <div className="h-2.5 w-1/2 rounded bg-surface-3" />
  </div>
</div>
```

### Inline Spinner
For buttons and small areas:
```jsx
<div className="w-5 h-5 border-2 border-primary-light border-t-transparent rounded-full animate-spin" />
```

## Pull-to-Refresh Pattern

Using touch events:
```jsx
const [pulling, setPulling] = useState(false)
const [refreshing, setRefreshing] = useState(false)
const startY = useRef(0)

const onTouchStart = (e) => { startY.current = e.touches[0].clientY }
const onTouchMove = (e) => {
  const diff = e.touches[0].clientY - startY.current
  if (diff > 60 && scrollRef.current.scrollTop === 0) setPulling(true)
}
const onTouchEnd = () => {
  if (pulling) { setRefreshing(true); refresh().finally(() => setRefreshing(false)) }
  setPulling(false)
}
```

## Gesture-Based Interactions

### Swipe to Delete/Action
```css
.swipeable {
  transition: transform 0.2s ease;
  touch-action: pan-y; /* Allow vertical scroll, capture horizontal */
}
```

### Bottom Sheet
Draggable overlay from bottom:
- Start at 50% height, draggable to full or dismissed
- Use `touch-action: none` on the drag handle
- Snap points: 0% (closed), 50% (half), 100% (full)

## Form Design

### Input Fields
- Minimum height: 44px
- Border radius: `rounded-xl` (12px)
- Background: `bg-surface-2`
- Focus: `focus:ring-1 focus:ring-primary-light/40 focus:outline-none`
- Label above, not floating (clearer on mobile)

### Range Sliders (Custom)
```css
input[type="range"] {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 24px;        /* touch target */
  background: transparent;
  cursor: pointer;
}
input[type="range"]::-webkit-slider-runnable-track {
  height: 3px;
  border-radius: 2px;
  background: var(--color-surface-3);
}
input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: var(--color-primary-light);
  margin-top: -5.5px;
}
```

## Platform Conventions

### Android
- Status bar height: ~24-36dp (varies by device)
- Navigation bar: ~48dp (buttons) or ~20dp (gesture bar)
- Material Design 3 ripple effects
- Back gesture from left edge
- No swipe-from-bottom-edge gesture (unlike iOS)

### iOS
- Status bar: 44pt (notch) or 20pt (no notch)
- Home indicator: 34pt
- Dynamic Island: varies
- Swipe from bottom to go home
- Swipe from left edge to go back
- Rubber-band scrolling (overscroll bounce)

## Dark Mode Design

For dark-first apps (media, entertainment, music):

```css
@theme {
  --color-bg: #0a0a0f;         /* Near black */
  --color-surface: #111118;     /* Slightly elevated */
  --color-surface-2: #1a1a24;   /* Card background */
  --color-surface-3: #252532;   /* Subtle borders/dividers */
  --color-text: #e8e8f0;        /* Primary text — not pure white */
  --color-text-dim: #8888a0;    /* Secondary text */
}
```

Principles:
- Never use pure black (#000) — use near-black (#0a0a0f)
- Never use pure white (#fff) for text — use off-white (#e8e8f0)
- Use surface color layers for depth instead of shadows
- Accent colors should be vibrant but not neon-bright
- Use opacity for layering: `bg-surface-2/40`, `border-surface-3/60`
