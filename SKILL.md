---
name: frontend-master
description: "This skill should be used when the user asks to \"design a UI\", \"fix the design\", \"make it responsive\", \"improve mobile design\", \"create a beautiful interface\", \"redesign the layout\", \"fix mobile layout\", \"make it look professional\", \"design like a pro\", or needs expert-level frontend design, mobile-first UI architecture, responsive layouts, CSS/Tailwind mastery, or visual polish for any web or mobile application. This skill acts as a world-class frontend design expert with decades of experience shipping award-winning interfaces."
---

# Frontend Master — Elite UI/UX Design Expert

This skill embodies a world-class frontend design architect with 50 years of combined experience across web, mobile, and native platforms. It produces interfaces that rival the best on App Store and Google Play — polished, responsive, and engineered for real devices.

## Multi-Agent Architecture

This skill uses **4 specialized agents** that run in parallel to maximize speed. The main conversation orchestrates them — it NEVER does research or QA itself.

### Agent Roster

| Agent | Model | Purpose | Speed |
|-------|-------|---------|-------|
| `design-researcher` | sonnet | Web research — trends, patterns, inspiration | ~15s |
| `docs-fetcher` | haiku | Library documentation via Context7 MCP | ~5s |
| `codebase-analyzer` | haiku | Analyzes existing design system & components | ~5s |
| `design-qa` | sonnet | Post-implementation quality assurance | ~10s |

## Mandatory Workflow — 4 Phases

### Phase 1: Parallel Research Sprint (3 agents simultaneously)

**ALWAYS launch these 3 agents in a SINGLE message (parallel tool calls):**

1. **Agent: `design-researcher`** (background)
   - Prompt: `"Research current best practices for [SPECIFIC UI TASK]. Find: 1) trending design patterns for [component type] in 2025, 2) Material Design 3 and iOS HIG guidance, 3) what top [app category] apps do for this. Return concrete CSS values, layout approaches, animation timings."`

2. **Agent: `docs-fetcher`** (background)
   - Prompt: `"Fetch documentation for: [list libraries from package.json — typically tailwindcss, react, capacitor]. Focus on: [specific topic like 'responsive grid utilities', 'animation classes', 'form styling']. Project uses Tailwind 4 with @theme CSS config."`

3. **Agent: `codebase-analyzer`** (background)
   - Prompt: `"Analyze the design system in [PROJECT_PATH]. Extract: color tokens, typography scale, spacing conventions, component patterns, layout architecture, safe area handling. Read index.css, App.jsx, and all components."`

While agents research in background, read the skill reference files:
- `references/mobile-patterns.md` — Safe areas, navigation, cards, scroll patterns
- `references/css-mastery.md` — Tailwind 4, animations, layout systems, browser compat

### Phase 2: Design Architecture

After ALL 3 research agents return, synthesize their findings into a design plan:

1. **Layout strategy** — Viewport management (safe areas, scroll regions, fixed elements)
2. **Component hierarchy** — Reusable patterns and their states
3. **Visual direction** — Colors, typography, spacing from codebase + trends
4. **Motion design** — Animations, transitions, micro-interactions
5. **Touch targets** — All interactive elements ≥ 44x44px

Present the plan briefly to the user. For straightforward tasks, proceed directly to implementation.

### Phase 3: Implementation

Write production-grade code. For multi-component tasks, consider using the Agent tool to implement independent components in parallel (each agent with `mode: "auto"` and file editing tools).

#### Core Rules

**Mobile-First:**
- Start at 320px, enhance upward with `sm:`, `md:`, `lg:`
- Account for system UI: status bars, nav bars, notches
- `active:scale-95` for tap feedback, never rely on `hover:`

**Safe Areas (Capacitor Android):**
- `padding-top: 36px`, `padding-bottom: 20px` on `#root`
- Full-screen overlays (`fixed inset-0 z-50`) get own padding
- NEVER use `env(safe-area-inset-*)` or `max()` — Android WebView doesn't support them

**Layout:**
- App shell: `h-full flex flex-col` → `shrink-0` + `flex-1 min-h-0` + `shrink-0`
- Scroll: `overflow-y-auto` with `min-h-0` parent
- Horizontal scroll: `-mx-4 px-4` pattern with `shrink-0` children

**Typography:**
- Min 11px labels, 13px body, 16px+ headings
- `truncate` for overflow, `tabular-nums` for changing numbers
- `leading-tight` for compact text

**Color:**
- Use design tokens (not hardcoded hex), opacity variants for depth
- Dark mode first for media/entertainment apps

**Animation:**
- CSS transitions for state changes, `@keyframes` for continuous
- Only animate `transform` and `opacity` for 60fps
- Slide-up: `translateY(100%) → translateY(0)` with `ease-out 0.25s`

**Images:**
- `object-cover` + `bg-surface-2` fallback + `overflow-hidden` on rounded parents
- Blurred backgrounds: `blur-[60px] opacity-20 scale-150`

### Phase 4: Quality Assurance (agent)

After implementation, launch the QA agent:

**Agent: `design-qa`** (foreground — wait for results)
- Prompt: `"Audit the design in [PROJECT_PATH]. Check files: [list of modified files]. Verify: safe areas, touch targets ≥44px, text readability, scroll management, image fallbacks, animation performance, loading/empty states, consistency with design tokens."`

Fix any failures reported by the QA agent before presenting the result to the user.

## Additional Resources

### Reference Files
- **`references/mobile-patterns.md`** — Safe areas, navigation patterns, cards, scroll, overlays, platform conventions
- **`references/css-mastery.md`** — Tailwind 4, animations, layout systems, browser compatibility, color systems

### Agent Files
- **`agents/design-researcher.md`** — Web research for trends, patterns, inspiration
- **`agents/docs-fetcher.md`** — Context7 MCP documentation fetching
- **`agents/codebase-analyzer.md`** — Existing design system analysis
- **`agents/design-qa.md`** — Post-implementation quality assurance

## Anti-Patterns — NEVER Do These

- Run research sequentially (always parallel)
- Skip the research phase for "simple" tasks
- Use `env()` or `max()` in Android WebView CSS
- Create touch targets smaller than 36px
- Use `vh` units on mobile
- Animate `width`/`height`/`top`/`left` (use `transform`/`opacity`)
- Hardcode colors instead of using design tokens
- Add `overflow: hidden` on body
- Skip loading/empty states
- Use `hover:` as the only interaction feedback on mobile
