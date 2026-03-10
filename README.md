# Frontend Master — Claude Code Skill

Elite frontend & mobile design skill for [Claude Code](https://claude.com/claude-code). Acts as a world-class UI/UX design expert with 50 years of combined experience, powered by 6 specialized agents running in parallel.

## What It Does

Takes any design task — from "fix the layout" to "redesign the entire app" — and executes a full pipeline:

1. **Researches** current trends, library docs, and your existing codebase (3 agents in parallel)
2. **Implements** production-grade, mobile-first code
3. **Audits** the code for design quality
4. **Screenshots** the real device via ADB over WiFi
5. **Visually reviews** the screenshot and iterates until perfect

## Agent Architecture

```
Phase 1: PARALLEL RESEARCH (~15s total)
  ├─ design-researcher (sonnet)     — Web trends, Material Design, iOS HIG
  ├─ docs-fetcher (haiku)           — Tailwind/React/Capacitor docs via Context7
  └─ codebase-analyzer (haiku)      — Existing design tokens, components, patterns

Phase 2: SYNTHESIZE → design plan

Phase 3: IMPLEMENT → production code

Phase 4: CODE QA
  └─ design-qa (sonnet)             — Touch targets, safe areas, accessibility

Phase 5: DEVICE FEEDBACK LOOP (max 3 iterations)
  ├─ device-screenshotter (haiku)   — Build → Install → ADB screenshot
  ├─ Main reads screenshot           — Multimodal visual check
  ├─ visual-reviewer (sonnet)       — Detailed analysis, scores 1-10 per category
  └─ Score < 8? → Fix → REPEAT
```

## Installation

Copy the skill to your Claude Code skills directory:

```bash
# Clone the repo
git clone https://github.com/MrQrax/frontend-master-skill.git

# Copy skill files
cp -r frontend-master-skill/ ~/.claude/skills/frontend-master/

# Copy the /design slash command
cp frontend-master-skill/commands-design.md ~/.claude/commands/design.md
```

## Usage

### Automatic Triggering
The skill triggers automatically when you say things like:
- "fix the design"
- "make it responsive"
- "improve mobile design"
- "redesign the layout"
- "make it look professional"

### Slash Command
```
/design create a settings page with dark mode toggle and user profile
```

## Features

### Parallel Research
Three agents launch simultaneously to gather context:
- **Web research** — Current UI/UX trends, platform guidelines, design inspiration
- **Documentation** — Up-to-date Tailwind CSS, React, Capacitor docs via Context7 MCP
- **Codebase analysis** — Your existing design tokens, component patterns, layout architecture

### Mobile-First Design
- Safe area management (Android WebView quirks handled)
- Touch targets ≥ 44px with press feedback
- Proper scroll management (`flex-1 min-h-0` pattern)
- Platform-specific conventions (Material Design 3, iOS HIG)

### Device Screenshot Loop
Connects to your Android phone via wireless ADB and creates a visual feedback loop:
```
Implement → Build APK → Install → Screenshot → Visual Review → Fix → Repeat
```
- Uses `adb exec-out screencap -p` for fast screenshots
- Claude reads the image (multimodal) for visual assessment
- `visual-reviewer` agent scores layout, typography, color, spacing (1-10)
- Iterates until all scores ≥ 8/10

### Code Quality Assurance
The `design-qa` agent checks:
- Safe area compliance (no content behind status/nav bar)
- Touch target sizes (≥ 44px)
- Text readability (contrast, font sizes)
- Scroll management
- Image handling (fallbacks, object-fit)
- Animation performance (transform/opacity only)
- Loading/empty/error states

## File Structure

```
frontend-master/
├── SKILL.md                          — Main skill (auto-triggers)
├── README.md                         — This file
├── commands-design.md                — /design slash command
├── agents/
│   ├── design-researcher.md          — Web research (sonnet)
│   ├── docs-fetcher.md               — Context7 MCP docs (haiku)
│   ├── codebase-analyzer.md          — Design system audit (haiku)
│   ├── design-qa.md                  — Code quality check (sonnet)
│   ├── device-screenshotter.md       — ADB build/install/screenshot (haiku)
│   └── visual-reviewer.md            — Multimodal visual analysis (sonnet)
└── references/
    ├── mobile-patterns.md            — Safe areas, navigation, cards, scroll
    └── css-mastery.md                — Tailwind 4, animations, layout, compat
```

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI
- Context7 MCP plugin (for documentation fetching)
- Android device with wireless ADB (for screenshot loop)
- Node.js + Capacitor project (for build/install pipeline)

## Tech Stack Expertise

- **CSS/Tailwind** — Tailwind CSS 4 with `@theme` config, responsive modifiers, custom tokens
- **React** — Hooks, component architecture, performance patterns
- **Capacitor** — Android WebView safe areas, native bridge, status bar control
- **Mobile Design** — Material Design 3, iOS HIG, touch interaction, gesture navigation

## License

MIT
