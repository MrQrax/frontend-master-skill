# CSS Mastery — Advanced Techniques Reference

## Tailwind CSS 4 — Key Differences

### CSS-First Configuration
Tailwind 4 uses `@theme` blocks in CSS instead of `tailwind.config.js`:

```css
@import "tailwindcss";

@theme {
  --color-primary: #6d28d9;
  --color-primary-light: #8b5cf6;
  --color-accent: #06b6d4;
  --color-surface: #111118;
  --color-bg: #0a0a0f;
  --color-text: #e8e8f0;
  --color-text-dim: #8888a0;
}
```

These tokens become utility classes automatically: `bg-primary`, `text-accent`, etc.

### Responsive Design Utilities
```
sm:  → 640px+
md:  → 768px+
lg:  → 1024px+
xl:  → 1280px+
2xl: → 1536px+
```

Mobile-first: base styles are for mobile, add `sm:`, `md:`, etc. for larger screens.

### Important Utility Patterns

**Flex Layout:**
- `flex flex-col` — vertical stack
- `flex items-center gap-3` — horizontal row with centered items
- `flex-1 min-h-0` — take remaining space AND allow overflow (critical for scroll)
- `shrink-0` — never compress this element
- `min-w-0` — allow flex child to truncate text

**Grid Layout:**
- `grid grid-cols-2 gap-2.5` — 2 column grid
- `grid grid-cols-[auto_1fr_auto]` — custom columns

**Spacing:**
- `gap-*` for flex/grid gaps
- `space-y-*` for vertical spacing between children
- `divide-y divide-surface-2` for separators between items

**Sizing:**
- `w-full h-full` — fill parent
- `w-[240px]` — arbitrary value
- `max-w-sm` — max width constraints
- `aspect-square` — 1:1 ratio

**Text:**
- `truncate` — single line with ellipsis
- `line-clamp-2` — two lines then ellipsis
- `text-[13px]` — arbitrary font size
- `tracking-wide` / `tracking-widest` — letter spacing
- `uppercase` — all caps
- `tabular-nums` — fixed-width numbers

**Borders & Shadows:**
- `rounded-xl` (12px), `rounded-2xl` (16px), `rounded-full`
- `border border-surface-3/40` — subtle border with opacity
- `shadow-xl` — elevated shadow
- `ring-1 ring-primary-light/40` — focus ring

**Backgrounds & Effects:**
- `bg-bg/80 backdrop-blur-md` — frosted glass effect
- `bg-gradient-to-br from-primary to-accent` — gradient
- `opacity-20` — transparency
- `pointer-events-none` — allow clicks through element

**Transitions & Animation:**
- `transition-colors` / `transition-transform` / `transition-all`
- `duration-200` / `duration-300`
- `ease-out` / `ease-in-out`
- `animate-spin` / `animate-pulse` / `animate-bounce`

## Advanced CSS Techniques

### Custom Animations

```css
@keyframes slide-up {
  from { transform: translateY(100%); }
  to { transform: translateY(0); }
}

@keyframes fade-in {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes eq-bar {
  0%, 100% { height: 30%; }
  50% { height: 100%; }
}

@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}

.animate-slide-up { animation: slide-up 0.25s ease-out; }
.animate-fade-in { animation: fade-in 0.3s ease-out; }
```

### Staggered Animations
```css
.stagger > :nth-child(1) { animation-delay: 0ms; }
.stagger > :nth-child(2) { animation-delay: 50ms; }
.stagger > :nth-child(3) { animation-delay: 100ms; }
.stagger > :nth-child(4) { animation-delay: 150ms; }
/* Or use inline styles for dynamic lists */
```

```jsx
{items.map((item, i) => (
  <div key={item.id} className="animate-fade-in" style={{ animationDelay: `${i * 50}ms` }}>
    {/* content */}
  </div>
))}
```

### Glassmorphism / Frosted Glass
```css
.glass {
  background: rgba(17, 17, 24, 0.8);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.06);
}
```

Tailwind: `bg-surface/80 backdrop-blur-md border border-white/5`

### Scroll Snap
```css
.scroll-snap-x {
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;
}
.scroll-snap-x > * {
  scroll-snap-align: start;
}
```

### Container Queries (Modern CSS)
```css
.card-container {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .card { /* wider layout */ }
}
```

### Custom Scrollbar Styling
```css
/* Hide completely on mobile */
::-webkit-scrollbar { width: 0; height: 0; }

/* Thin custom scrollbar for desktop */
@media (min-width: 768px) {
  ::-webkit-scrollbar { width: 6px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb {
    background: var(--color-surface-3);
    border-radius: 3px;
  }
}
```

### Custom Range Input
```css
input[type="range"] {
  -webkit-appearance: none;
  appearance: none;
  background: transparent;
  cursor: pointer;
  width: 100%;
  height: 24px; /* touch target height */
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
  margin-top: -5.5px; /* (track-height - thumb-height) / 2 */
}

/* Firefox */
input[type="range"]::-moz-range-track {
  height: 3px;
  border-radius: 2px;
  background: var(--color-surface-3);
}

input[type="range"]::-moz-range-thumb {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: var(--color-primary-light);
  border: none;
}
```

### Progress Bar with Gradient Fill
```css
.progress-track {
  height: 3px;
  background: var(--color-surface-3);
  border-radius: 2px;
  overflow: hidden;
}
.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, var(--color-primary), var(--color-accent));
  border-radius: 2px;
  transition: width 0.2s linear;
}
```

## Layout Systems

### Full-Height App Shell
The foundation of every mobile app:

```css
html, body, #root {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
```

```jsx
<div className="h-full flex flex-col">
  <header className="shrink-0">{/* Fixed */}</header>
  <main className="flex-1 min-h-0 overflow-y-auto">{/* Scrolls */}</main>
  <footer className="shrink-0">{/* Fixed */}</footer>
</div>
```

**Why `min-h-0`?** Flex items have `min-height: auto` by default, which means they won't shrink below their content size. `min-h-0` allows the flex item to be smaller than its content, enabling `overflow-y-auto` to actually scroll.

### Sticky Headers Inside Scroll
```jsx
<main className="flex-1 min-h-0 overflow-y-auto">
  <div className="sticky top-0 z-10 bg-bg/90 backdrop-blur-md px-4 py-2">
    {/* Sticky section header */}
  </div>
  {/* Scrollable content */}
</main>
```

### Absolute Positioning for Overlays
```jsx
<div className="relative">
  {/* Base content */}
  <img src={cover} className="w-full h-full object-cover" />

  {/* Overlay */}
  <div className="absolute inset-0 bg-black/50 flex items-center justify-center">
    {/* Overlay content */}
  </div>

  {/* Bottom badge */}
  <div className="absolute bottom-2 right-2 bg-bg/70 backdrop-blur rounded-full px-2 py-0.5">
    <span className="text-[10px]">3:45</span>
  </div>
</div>
```

## Color System Design

### Token Structure
```
bg          → app background (darkest)
surface     → elevated containers
surface-2   → cards, inputs
surface-3   → borders, dividers, placeholders
text        → primary text
text-dim    → secondary text, labels
primary     → brand color, main actions
primary-light → lighter variant, active states
accent      → secondary action color
```

### Opacity Pattern for Depth
Instead of defining 20 color variants, use opacity:
```
bg-surface-2      → solid card
bg-surface-2/80   → semi-transparent card (with blur)
bg-surface-2/40   → subtle highlight (active state)
border-surface-3/40 → subtle border
text-text-dim/60  → muted text
```

### Semantic Color Usage
```
Action buttons    → bg-primary-light text-white
Active/selected   → text-primary-light, bg-primary-light (for pills)
Destructive       → text-red-500
Success           → text-emerald-500
Warning           → text-amber-500
Inactive          → text-text-dim/60
Favorite/love     → text-neon-pink (#ec4899)
```

## Performance Optimization

### CSS Containment
```css
.card {
  contain: layout style; /* Isolates layout calculations */
}
```

### Will-Change for Animations
```css
.animate-slide-up {
  will-change: transform;
  animation: slide-up 0.25s ease-out;
}
```
Remove `will-change` after animation completes to free GPU memory.

### Hardware Acceleration
Use `transform: translateZ(0)` or `transform: translate3d(0,0,0)` to force GPU compositing for smooth animations.

### Avoid Layout Thrashing
- Read DOM properties (offsetHeight, scrollTop) together, then write together
- Use `transform` and `opacity` for animations (compositor-only properties)
- Avoid animating `width`, `height`, `top`, `left` (trigger layout recalculation)

## Browser Compatibility Notes

### Android WebView (Capacitor)
- ✅ Flexbox, Grid, Custom Properties
- ✅ `backdrop-filter` (with `-webkit-` prefix)
- ✅ `object-fit`, `aspect-ratio`
- ✅ CSS animations and transitions
- ✅ `overscroll-behavior`
- ❌ `env(safe-area-inset-*)` — unreliable
- ❌ `max()` CSS function — unreliable on older WebViews
- ❌ `container queries` — not on Android < 14 WebView
- ❌ `has()` selector — limited support

### iOS Safari WebView
- ✅ Everything above
- ✅ `env(safe-area-inset-*)`
- ✅ `max()`, `min()`, `clamp()`
- ⚠️ `position: fixed` — quirky with keyboard open
- ⚠️ `100vh` — includes address bar (use `100dvh` instead)

### Modern CSS to Use Freely
These work in all modern mobile WebViews:
- `gap` in flexbox
- CSS Grid with named areas
- `aspect-ratio`
- `accent-color` for form elements
- `color-mix()` for color blending
- Logical properties (`margin-inline`, `padding-block`)
- `@layer` for cascade management
