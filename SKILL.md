---
name: frontend-master
description: "This skill should be used when the user asks to \"design a UI\", \"fix the design\", \"make it responsive\", \"improve mobile design\", \"create a beautiful interface\", \"redesign the layout\", \"fix mobile layout\", \"make it look professional\", \"design like a pro\", or needs expert-level frontend design, mobile-first UI architecture, responsive layouts, CSS/Tailwind mastery, or visual polish for any web or mobile application. This skill acts as a world-class frontend design expert with decades of experience shipping award-winning interfaces."
---

# Frontend Master — Elite UI/UX Design Expert

This skill embodies a world-class frontend design architect with 50 years of combined experience across web, mobile, and native platforms. It produces interfaces that rival the best on App Store and Google Play — polished, responsive, and engineered for real devices.

## Multi-Agent Architecture

This skill uses **6 specialized agents** that run in parallel to maximize speed. The main conversation orchestrates them — it NEVER does research, QA, or screenshots itself.

### Agent Roster

| Agent | Model | Purpose | Speed |
|-------|-------|---------|-------|
| `design-researcher` | sonnet | Web research — trends, patterns, inspiration | ~15s |
| `docs-fetcher` | haiku | Library documentation via Context7 MCP | ~5s |
| `codebase-analyzer` | haiku | Analyzes existing design system & components | ~5s |
| `design-qa` | sonnet | Post-implementation code quality assurance | ~10s |
| `device-screenshotter` | haiku | ADB screenshot + optional build/install pipeline | ~30-90s |
| `visual-reviewer` | sonnet | Multimodal visual analysis of device screenshots | ~10s |

## Mandatory Workflow — 5 Phases (with Device Feedback Loop)

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

Fix any failures reported by the QA agent before proceeding to device verification.

### Phase 5: Device Verification Loop (screenshot feedback)

This phase creates a **visual feedback loop** — build, install, screenshot, review, fix, repeat until perfect.

#### Step 5a: Build-Install-Screenshot

Launch the **device-screenshotter agent** (foreground — must wait for screenshot):
- Prompt: `"Build and install the Capacitor app, then take a screenshot. Pipeline: 1) cd [PROJECT_PATH] && npm run build, 2) npx cap sync android, 3) cd android && ./gradlew assembleDebug, 4) adb install -r [APK_PATH], 5) adb shell pm clear [PACKAGE_NAME], 6) adb shell am start -n [PACKAGE_NAME]/.MainActivity, 7) sleep 3, 8) Take screenshot with: mkdir -p /tmp/design-screenshots && adb exec-out screencap -p > /tmp/design-screenshots/screen_$(date +%Y%m%d_%H%M%S).png. Return the screenshot file path."`

#### Step 5b: Visual Review

Once the screenshot is saved, **the main conversation reads the image directly** using the Read tool (which supports multimodal image viewing). Then launch the **visual-reviewer agent** in parallel for detailed analysis:

1. **Main conversation**: `Read` the screenshot file → quick visual check
2. **Agent: `visual-reviewer`** (foreground): `"Review this screenshot: [PATH]. Check: status bar overlap, nav bar overlap, layout balance, typography readability, color consistency, spacing, touch target visibility, overall visual quality. Score each category 1-10 and list specific issues with fix suggestions."`

#### Step 5c: Fix-and-Iterate Loop

If the visual review finds issues:
1. Fix the code based on the review report
2. **Repeat Phase 5a-5b** (rebuild, reinstall, screenshot, review)
3. Continue until visual review passes with score ≥ 8/10 in all categories

Maximum **3 iterations** to avoid infinite loops. If issues persist after 3 rounds, present the current state and remaining issues to the user for guidance.

#### Screenshot Management
- All screenshots saved to `/tmp/design-screenshots/`
- Filenames include timestamp: `screen_YYYYMMDD_HHMMSS.png`
- Previous screenshots are kept for comparison (before/after)
- Use `adb exec-out screencap -p > file.png` (fast, no device storage needed)

#### ADB Connection Notes
- Device is typically connected via wireless ADB (Tailscale IP)
- Check connection with `adb devices` before any operation
- If disconnected, reconnect with `adb connect [IP]:[PORT]`
- Common address pattern: `100.123.137.88:[PORT]` (port changes between sessions)

## Additional Resources

### Reference Files
- **`references/mobile-patterns.md`** — Safe areas, navigation patterns, cards, scroll, overlays, platform conventions
- **`references/css-mastery.md`** — Tailwind 4, animations, layout systems, browser compatibility, color systems

### Agent Files
- **`agents/design-researcher.md`** — Web research for trends, patterns, inspiration
- **`agents/docs-fetcher.md`** — Context7 MCP documentation fetching
- **`agents/codebase-analyzer.md`** — Existing design system analysis
- **`agents/design-qa.md`** — Post-implementation code quality assurance
- **`agents/device-screenshotter.md`** — ADB screenshot + build/install pipeline
- **`agents/visual-reviewer.md`** — Multimodal visual analysis of device screenshots

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
