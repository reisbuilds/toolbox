# Component Suggestions
## design-lifecycle · Claude Code Skill
> Structural specification grounded in the Project Brief and Inspiration Summary. All token references correspond to `design-tokens.md`.

---

## Component Hierarchy

### Primitives (Atoms)

These components have no dependencies on other components. They define the visual vocabulary.

---

#### Button

Variants: `primary`, `secondary`, `ghost`, `destructive`  
Sizes: `sm`, `md`, `lg`

```
Variant     | Bg                 | Text             | Border                  | Hover / Active
----------- | ------------------ | ---------------- | ----------------------- | ---------------------------------
primary     | --accent           | #fff             | none                    | bg → --accent-dark; scale 0.98
secondary   | --surface-up       | --text           | 1px --border            | border → --border-active
ghost       | transparent        | --text-mid       | none                    | bg → --surface; text → --text
destructive | --error-subtle     | --error-text     | 1px --error (20% alpha) | bg → rgba(239,68,68,0.20)
```

```
Size | Height | H-Padding | Font                  | Radius
---- | ------ | --------- | --------------------- | ---------
sm   | 32px   | 12px      | label-md (13px, 500)  | radius-md (8px)
md   | 40px   | 16px      | label-md (14px, 500)  | radius-md (8px)
lg   | 48px   | 20px      | label-lg (16px, 600)  | radius-md (8px)
```

States: `default` → `hover` → `active` → `focus` (shadow-accent ring) → `disabled` (opacity 0.4, pointer-events none).  
Never use `outline: none` without substituting `box-shadow: var(--shadow-accent)` on focus.

Copy-to-clipboard variant: `ghost sm` with a 16px copy icon. On click, icon swaps to checkmark for 1500ms then reverts.

---

#### Input / Textarea / Select

All share the same visual baseline:
- Background: `--surface`
- Border: `1px solid --border`
- Border-radius: `radius-sm` (6px)
- Padding: `10px 14px`
- Font: `text-sm` (14px), `--font-sans`
- Placeholder: `--text-dim`
- Focus: border → `--border-accent`, `box-shadow: var(--shadow-accent)`
- Error: border → `--error` (40% alpha), error message below in `text-xs --error-text`

Mono input variant: for file path inputs and skill name fields, switch `font-family` to `--font-mono` and reduce font-size to 13px.

---

#### Badge / Tag / Chip

Three distinct use cases:

**Badge** — status label (used in nav, output lists):
- Background: `--surface-up`
- Border: `1px solid --border`
- Border-radius: `radius-full`
- Padding: `3px 10px`
- Font: `label-sm` (11px, 700, uppercase, +0.08em tracking)
- Text: `--text-mid`

**Tag** — attribute label on a card (used on feature cards, artifact cards):
- No background; border only
- Border: `1px solid --border`
- Border-radius: `radius-xs` (4px)
- Padding: `2px 7px`
- Font: `code-sm` (12px mono, 500)
- Text: `--text-dim`

**Status Chip** — pipeline phase status (icon + label pair — Carbon Design System principle):
- Carries icon + text. Never color alone.
- Background: semantic subtle (`--success-subtle`, `--warning-subtle`, `--error-subtle`, or `--info-subtle`)
- Border: 1px matching semantic color at 30% opacity
- Border-radius: `radius-full`
- Padding: `4px 10px 4px 8px`
- Gap between icon and text: 6px
- States and icons:

  | State     | Icon glyph | Color token         | Label example      |
  |-----------|------------|---------------------|--------------------|
  | pending   | ○          | `--text-dim`        | Pending            |
  | running   | ⟳ (spin)   | `--info`            | Running…           |
  | complete  | ✓          | `--success-text`    | Complete           |
  | warning   | ⚠          | `--warning-text`    | Needs attention    |
  | failed    | ✕          | `--error-text`      | Failed             |

---

#### Avatar

Sizes: 24px, 32px, 40px, 56px, 72px (matches `.agent-icon` in prototype).  
Shape: `radius-full` (circle).  
For agent icons specifically: 72px circle, `--surface` background, `1.5px solid --border`. On hover: border → `--accent`, `box-shadow: var(--shadow-accent)`, `transform: translateY(-2px)`.  
For user-facing contexts: supports image, initials fallback, or emoji glyph.

---

#### Icon Wrapper

Standardized container for all icons:

```
Size token | Wrapper size | Icon size | Border-radius
---------- | ------------ | --------- | -------------
icon-xs    | 20px         | 12px      | radius-sm (6px)
icon-sm    | 28px         | 16px      | radius-sm (6px)
icon-md    | 36px         | 20px      | radius-md (8px)
icon-lg    | 44px         | 24px      | radius-md (8px)
icon-xl    | 56px         | 28px      | radius-lg (12px)
```

Semantic icon wrappers add the semantic background fill (e.g., `--success-subtle` for a success icon). Non-semantic wrappers use transparent background, `--text-mid` icon color.

---

#### Divider

Horizontal: `border-top: 1px solid var(--border)`, no margin (parent controls spacing).  
Vertical: `width: 1px; background: var(--border); align-self: stretch`.  
With label: centered `text-xs --text-dim` label with 16px horizontal gap; divider lines extend to edges.

---

### Composed Components (Molecules)

---

#### NavBar

Fixed top, `z-index: 100`. Full-width. Uses `backdrop-filter: blur(12px)` on `rgba(8,12,20,0.85)` background.

```
┌─────────────────────────────────────────────────────────────┐
│  design-lifecycle          Features  Install  Pipeline  ···  │  [Copy install command]
└─────────────────────────────────────────────────────────────┘
  ↑ nav-brand (mono, --accent-text)    ↑ nav-links (4–5 items)   ↑ primary CTA (Button sm)
```

- Height: 56px
- Brand: `--font-mono`, 14px, 500, `--accent-text`
- Nav links: `text-sm` (14px), `--text-mid` → `--text` on hover; active link: `--accent-text` with 2px bottom border `--accent`
- CTA: `Button primary sm` right-anchored
- Mobile: links collapse behind a menu toggle; brand and CTA remain visible

Anchor link behavior: scrolling past a section updates the active nav link via IntersectionObserver.

---

#### HeroInstallBlock

The primary CTA — the install command treated as the hero action (Homebrew pattern from Inspiration Summary).

```
┌──────────────────────────────────────────────────────────────┐
│  $ cp -r skills/design-lifecycle ~/.claude/skills/            │  [Copy]
└──────────────────────────────────────────────────────────────┘
```

- Background: `--surface`
- Border: `1px solid --border`
- Border-radius: `radius-lg` (12px)
- Padding: `14px 22px`
- Font: `--font-mono`, `code-md` (14px)
- Prompt `$`: `--text-dim`, `user-select: none`
- Command: `--accent-text`
- Arguments/paths: `--text-mid`
- Copy icon button: `ghost sm` Button, 16px icon, right-aligned
- On copy: icon swaps to ✓ for 1500ms

This is the only element allowed to appear without a label or preceding header. It speaks for itself.

---

#### TerminalBlock

For multi-line command output, installation steps, or skill invocation examples. Distinct from HeroInstallBlock (which is single-line, interactive).

```
┌─ Terminal ────────────────────────────────────────────────────────────────┐
│  $ /design-lifecycle authentication flow                                  │
│                                                                           │
│  ⟳  Phase 01 · Thinking Partner      Running…                            │
│  ○  Phase 02 · Inspiration           Pending                              │
│  ○  Phase 03 · Maker                 Pending                              │
│  ○  Phase 04 · Verifier              Pending                              │
│  ○  Phase 05 · Amplifier             Pending                              │
└───────────────────────────────────────────────────────────────────────────┘
```

- Background: `--bg` (darker than card, simulating a terminal inset)
- Border: `1px solid --border`
- Border-radius: `radius-lg`
- Header strip: 28px, `--surface`, 3 dot decorators left, copy button right
- Font: `--font-mono`, 13px
- Line height: 1.7 (comfortable reading for wrapped output)

---

#### PipelineChecklist

The named UI pattern specific to this product. A vertical list of phase rows.

Each row is a `PhaseStatusRow` (see Feature Components). The list as a whole communicates overall run progress at a glance.

```
Phase 01  🧠 Thinking Partner     ✓ Complete         00:04
Phase 02  ✦ Inspiration           ✓ Complete         00:31
Phase 03  ⚒  Maker                ⟳ Running…         00:07
Phase 04  ◉  Verifier             ○ Pending          —
Phase 05  ⚡ Amplifier             ○ Pending          —
```

Full specification in Feature Components section below.

---

#### FeatureCard

For the "what you get" feature grid (2-column on desktop, 1-column on mobile).

```
┌───────────────────────────────────────────┐
│  ⚒  [icon-md, --surface-up bg]            │
│                                           │
│  Design Tokens                            │  ← label-lg (16px 600)
│  Hex, HSL, semantic roles, CSS custom     │  ← text-sm --text-mid
│  property reference block.               │
│                                           │
│  [tokens]  [CSS]  [design-tokens.md]      │  ← Tag components
└───────────────────────────────────────────┘
```

- Background: `--surface`
- Border: `1px solid --border`
- Border-radius: `radius-lg`
- Padding: `space-7` (28px)
- Hover: border → `--border-active`; no background change (restraint)
- Icon: `icon-lg` (44px) wrapper, `--surface-up` background, no border
- Heading: `label-lg`, `--text`
- Body: `text-sm`, `--text-mid`, line-height 1.65
- Footer tags: `Tag` component, left-aligned, `gap: space-2` (8px)

---

#### OutputFileCard

Represents a generated artifact file. Used in the Artifact Consumption flow.

```
┌──────────────────────────────────────────────────────────────────────┐
│  .design/design-tokens.md                │  Token definition file…   │  [TOKENS]
│  (mono, --accent-text, bg --bg)          │  (text-sm --text-mid)     │  (label-sm --text-dim)
└──────────────────────────────────────────────────────────────────────┘
```

Three-column layout (matches prototype `.output-item`):
- Col 1 (260px): File path — `--font-mono`, 12px, `--accent-text`, `--bg` background
- Col 2 (1fr): Description — `text-sm`, `--text-mid`
- Col 3 (80px): Type tag — `label-sm`, `--text-dim`, uppercase, centered, left-border

Border: `1px solid --border`. Hover: border → `--border-active`. `radius-lg`. No background fill on hover (border change is sufficient on a file-list pattern).

Mobile: stacks to single column; col-1 has bottom border, col-3 has top border.

---

#### ContractDataViz

The data contract table showing agent → receives/emits relationships.

```
┌──────────────────┬────────────────────────────────────────────────────┐
│  AGENT           │  DATA FLOW                                         │
├──────────────────┼────────────────────────────────────────────────────┤
│  Thinking        │  ← [receives: codebase]  → [emits: DESIGN_BRIEF]  │
│  Inspiration     │  ← [receives: BRIEF]     → [emits: INSPO_SUMMARY] │
│  Maker           │  ← [receives: BRIEF + INSPO] → [emits: TOKENS]    │
│  Verifier        │  ← [receives: TOKENS]    → [emits: VERIFY_REPORT] │
│  Amplifier       │  ← [receives: all]       → [emits: DOCUMENTATION] │
└──────────────────┴────────────────────────────────────────────────────┘
```

- Outer container: `--surface`, `1px solid --border`, `radius-lg`, `overflow: hidden`
- Header row: `--bg` background, `label-sm` column labels, `--text-dim`
- Data rows: min-height 52px, hover → `--surface-up` background
- Agent name cell: 160px, `text-sm` 500 `--text-mid`, right border
- Data flow cell: flex row, `gap: space-2`, wraps on overflow
- Pills: `pill-receives` (accent fill) for inputs, `pill-emits` (success fill) for outputs
- Pill font: `--font-mono`, 11px, 500

---

#### StatusStat

A single metric display (for hero stats row and status grid).

```
5
specialized agents       ← text-xs --text-mid, font-variant-numeric: tabular-nums
```

- Value: `display-xl` (for hero) or `text-xl` (for grid), weight 700, `--text`, letter-spacing -0.03em
- Label: `text-xs` (13px), `--text-mid`
- No border, no background — whitespace carries the weight

For the status grid variant: wrapped in a `StatusCard` (`--surface`, `1px border`, `radius-lg`, `24px padding`). Adds `label-sm` eyebrow above the value.

---

#### Toast / Snackbar

Appears bottom-right. `z-index: 200`. Max-width 360px.

```
┌─────────────────────────────────────────┐
│  ✓  Command copied to clipboard          │  [×]
└─────────────────────────────────────────┘
```

- Background: `--surface-overlay` (`#1E293B`)
- Border: `1px solid --border-active`
- Border-radius: `radius-lg`
- Padding: `14px 18px`
- Shadow: `shadow-md`
- Icon: status chip icon, left-aligned (16px)
- Text: `text-sm` (14px), `--text`
- Dismiss: ghost × button, right-aligned
- Duration: 3000ms auto-dismiss with 300ms ease-out slide-up entry, 200ms fade exit
- Variants: `success` (green icon), `error` (red icon), `info` (accent icon)

---

### Feature Components (Organisms)

---

## Flow 1: Discovery

#### HeroSection

Full-width, `--bg`, `100px 0 80px padding`, relative positioned with radial gradient glow behind.

```
┌──────────────────────────────────────────────────────────────────────────┐
│                                                                          │
│  ○ Multi-agent orchestration                ← hero-eyebrow pill          │
│                                                                          │
│  Full Double Diamond.                       ← display-2xl (80px 800)     │
│  One command.                               ← display-2xl, --accent-text │
│                                                                          │
│  Five specialized agents. Four files…       ← text-lg (19px) --text-mid  │
│                                                                          │
│  ┌─────────────────────────────────────┐                                 │
│  │ $ /design-lifecycle  auth flow  [⧉] │   ← HeroInstallBlock            │
│  └─────────────────────────────────────┘                                 │
│                                                                          │
│  ─────────────────────────────────────────  ← hairline border-top        │
│                                                                          │
│  5                  4                  6           0                     │
│  specialized        files written      checks      design knowledge       │
│  agents             to disk            run         in orchestrator        │
│                     ← StatusStat × 4, flex row, gap: 40px                │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

#### FeatureGrid

2-column grid of `FeatureCard` components. Section header: `eyebrow` + `section-title` + `section-body`. Grid gap: 24px. On mobile: single column.

Minimum 4 cards for this product:
1. Design Tokens — hex, HSL, CSS custom properties block
2. Component Spec — hierarchy, variants, ASCII wireframes
3. Verification Report — contrast, spacing, accessibility checks
4. DESIGN_BRIEF.md — the final synthesis document

#### PipelineTrack

The horizontal 5-phase visual (matches prototype `.pipeline-track`). Grid of 5 `AgentNode` cells with a horizontal connector line.

```
          ──────────────────── progress line (accent, 40% opacity) ────────────────────
  🧠          ✦             ⚒              ◉              ⚡
  Phase 01    Phase 02      Phase 03       Phase 04       Phase 05
  Thinking    Inspiration   Maker          Verifier       Amplifier
  Discover    Mobbin        Develop        Before         Deliver
              & Define      WebSearch                     Delivery    & Operate
  → Brief     → Summary     → Tokens       → Report       → BRIEF.md
```

Each `AgentNode`: 72px circle icon (emoji glyph), mono phase number below, agent name, phase label, hover-reveal output label. Connector: `::before` pseudo, `1px solid --border`. Progress fill: `::after` pseudo, accent gradient, width = (completed phases / total phases) × track width.

---

## Flow 2: Installation

#### InstallSection

Standalone section with: eyebrow label, `section-title`, `section-body`, then the three steps as a vertical ordered list.

```
INSTALL
──────────────────────────────────────────────────────────────────
  Step 1. Copy the skill

  ┌──────────────────────────────────────────────────────────┐
  │ $ cp -r skills/design-lifecycle ~/.claude/skills/   [⧉] │
  └──────────────────────────────────────────────────────────┘

  Step 2. (Optional) Configure Mobbin MCP

  ┌──────────────────────────────────────────────────────────┐
  │ $ claude mcp add mobbin                             [⧉] │
  └──────────────────────────────────────────────────────────┘

  Step 3. Verify installation

  ┌──────────────────────────────────────────────────────────┐
  │ $ ls ~/.claude/skills/design-lifecycle/             [⧉] │
  └──────────────────────────────────────────────────────────┘
```

Each step: mono step number (label-sm, --accent, +0.05em tracking) → bold step heading (label-lg) → optional `text-sm` clarifying copy → `HeroInstallBlock` (single-line variant).

#### OptionalConfigCallout

An inset callout card for the Mobbin MCP optional step.

```
┌──────────────────────────────────────────────────────────────┐
│  ℹ  Optional — Mobbin MCP                                    │
│                                                              │
│  Without Mobbin, the Inspiration agent falls back to         │
│  WebSearch. Installation is faster; coverage is narrower.   │
└──────────────────────────────────────────────────────────────┘
```

- Background: `--info-subtle`
- Border: `1px solid rgba(59,130,246,0.25)`
- Border-radius: `radius-md` (8px)
- Padding: `space-4` (16px) `space-5` (20px)
- Icon: info `icon-sm` wrapper, `--info` color
- Heading: `label-md`, `--text`
- Body: `text-sm`, `--text-mid`

---

## Flow 3: Invocation

#### InvocationExplainer

Demonstrates the `/design-lifecycle` slash command with an optional focus argument.

```
INVOCATION
────────────────────────────────────────────────────────────────
  ┌─ Claude Code session ─────────────────────────────────────────┐
  │                                                               │
  │  > /design-lifecycle                                          │  ← no argument: full project
  │  > /design-lifecycle  authentication flow                     │  ← with focus
  │  > /design-lifecycle  payment checkout                        │
  │  > /project-showcase                                          │
  │                                                               │
  └───────────────────────────────────────────────────────────────┘
```

Uses `TerminalBlock` component. Argument portion styled `--text-mid`; command styled `--accent-text`. A short prose paragraph above explains the optional focus argument with an inline `code` element.

#### ArgumentAnnotation

A callout that visually deconstructs the command:

```
  /design-lifecycle  authentication flow
  ↑                  ↑
  skill name         focus argument (optional)
  required           narrows scope; omit for full project
```

Implemented as relative-positioned wrapper with `::before` connector lines and `text-xs --text-dim` annotations below. This is a decorative element, not interactive.

---

## Flow 4: Pipeline Monitoring

#### PhaseStatusRow

The atomic unit of the pipeline checklist. Icon + label + sub-status + elapsed time.

```
 [icon]  Phase 01 · Thinking Partner     ✓ Complete      00:04
 ↑       ↑                               ↑               ↑
 72px    label-md (13px 600)             StatusChip      code-xs --text-dim
 circle  --text                          component       tabular-nums
```

Three elevation states (the 3-step surface ladder design move):
- **Upcoming**: background `--bg` (base), border `--border`, icon opacity 0.5
- **Active**: background `--surface-up` (elevated), border `--border-accent`, icon at full opacity, subtle left border highlight `3px solid --accent`
- **Complete**: background `--surface` (card), border `--border`, icon opacity 1

Transitions: 150ms ease-out for background, border-color, and opacity changes.

Active phase left-border accent:
```css
.phase-row--active {
  border-left: 3px solid var(--accent);
  padding-left: calc(var(--sp-5) - 2px); /* compensate for border width */
}
```

Phase icon animation on completion: scale from 1 → 1.15 → 1 over 120ms ease-out (scale-up pop).

#### PipelineChecklistPanel

The full monitoring surface wrapping all five `PhaseStatusRow` components.

```
┌──────────────────────────────────────────────────────────────────────────┐
│  Pipeline  ·  design-lifecycle  ·  authentication flow                   │
│  Started 14:22:07  ·  Phase 3 of 5  ·  Running                          │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐  │
│  │  ✓  Phase 01  ·  Thinking Partner         Complete      00:04      │  │  ← --surface bg
│  │  ✓  Phase 02  ·  Inspiration              Complete      00:31      │  │  ← --surface bg
│  │  ⟳  Phase 03  ·  Maker                    Running…      00:07      │  │  ← --surface-up bg (ACTIVE)
│  │  ○  Phase 04  ·  Verifier                 Pending       —          │  │  ← --bg (upcoming)
│  │  ○  Phase 05  ·  Amplifier                Pending       —          │  │  ← --bg (upcoming)
│  └────────────────────────────────────────────────────────────────────┘  │
│                                                                          │
│  Overall progress: ████████████░░░░░░░░░░░░  3 / 5 phases  60%          │
└──────────────────────────────────────────────────────────────────────────┘
```

- Outer panel: `--surface`, `1px solid --border`, `radius-xl` (16px), `32px` padding
- Header: `label-md --text-mid`, pipe-separated metadata; `StatusChip running` right-anchored
- Phase list: `display: flex; flex-direction: column; gap: 0` — rows share borders
- Progress bar: thin `4px` strip, `--surface-up` track, `--accent` fill at width%, `radius-full`
- Progress label: `code-xs --text-dim`, tabular-nums, right-aligned

#### PhaseOutputPreview

Slides in below an active or completed `PhaseStatusRow` on expand. Shows truncated output from that phase.

```
  ▼  Phase 03 · Maker — Output
  ─────────────────────────────────────────
  Tokens defined: 34  Components: 12  Layouts: 5
  Writing: .design/design-tokens.md
  Writing: .design/component-suggestions.md
```

Background: `--bg`, 12px mono font, `--text-dim` for output lines, `--accent-text` for file paths. 200ms slide-down reveal with `overflow: hidden` + `max-height` transition.

---

## Flow 5: Artifact Consumption

#### ArtifactSection

Full `OutputFileCard` list, preceded by section header and brief explanatory copy.

```
OUTPUT ARTIFACTS
────────────────────────────────────────────────────────────────────────
  Four files written to your repository on every successful run:

  ┌──────────────────────────────────────────────────────────────────────┐
  │  .design/design-tokens.md        │  Visual foundation…    │  TOKENS  │
  ├──────────────────────────────────┼────────────────────────┼──────────┤
  │  .design/component-suggestions.md│  Component hierarchy…  │  SPEC    │
  ├──────────────────────────────────┼────────────────────────┼──────────┤
  │  .design/verification-report.md  │  6 checks run…         │  QA      │
  ├──────────────────────────────────┼────────────────────────┼──────────┤
  │  DESIGN_BRIEF.md                 │  Final synthesis…      │  BRIEF   │
  └──────────────────────────────────────────────────────────────────────┘
```

Each row is `OutputFileCard`. List spacing: `gap: 10px` (matches prototype's `margin-bottom: 10px`).

#### ArtifactDetailPanel

Expanded view for a single artifact. Triggered by clicking an `OutputFileCard`. Slides open inline below the row (not a modal — keeps context).

```
  ▼  .design/design-tokens.md

  ┌─ Preview ────────────────────────────────────────────────────────────┐
  │  # Design Tokens                                                     │
  │  ## design-lifecycle · Claude Code Skill                             │
  │                                                                      │
  │  ---                                                                 │
  │                                                                      │
  │  ## Color Palette                                                    │
  │  ...                                                                 │
  └──────────────────────────────────────────────────────────────────────┘
  [Open in editor]  [Copy path]
```

- Background: `--bg`
- Border: `1px solid --border`, `radius-md` inset, top border omitted (continues from parent row)
- Content: `TerminalBlock` with monospace preview, syntax-soft-colored (file headers `--accent-text`, body `--text-mid`, code values `--text`)
- Footer: two ghost buttons — "Open in editor" (triggers system open) and "Copy path" (clipboard)

---

## Key Screen Layouts

### Layout 1: Discovery (Landing Page)

```
┌────────────────────────────────────────────────────────────────────────────┐
│  NAV: design-lifecycle    Features · Install · Pipeline · Artifacts   [CTA] │  ← sticky, 56px
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  HERO                                                     (full-bleed bg)  │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │  ○ Multi-agent orchestration                                         │  │
│  │                                                                      │  │
│  │  Full Double Diamond.                                                │  │
│  │  One command.                                                        │  │
│  │                                                                      │  │
│  │  [ HeroInstallBlock ]                                                │  │
│  │                                                                      │  │
│  │  ──────────────────────────────────────────────                      │  │
│  │  5 agents  ·  4 files  ·  6 checks  ·  0 design knowledge          │  │
│  └──────────────────────────────────────────────────────────────────────┘  │
│                                                                            │
│  ─────── divider ──────────────────────────────────────────────────────    │
│                                                                            │
│  PIPELINE TRACK (horizontal, 5 agents)                                     │
│  [ 🧠 ] ──── [ ✦ ] ──── [ ⚒ ] ──── [ ◉ ] ──── [ ⚡ ]                      │
│                                                                            │
│  ─────── divider ──────────────────────────────────────────────────────    │
│                                                                            │
│  PROBLEM (--surface bg, full-width section)                                │
│  ┌ 2-column grid ─────────────────────────────────────────────────────┐    │
│  │  Blockquote (26px)         │  Body text + symptom list             │    │
│  └────────────────────────────────────────────────────────────────────┘    │
│                                                                            │
│  HOW IT WORKS                                                              │
│  ┌ 2-column grid ─────────────────────────────────────────────────────┐    │
│  │  FeatureCard  FeatureCard  │  FeatureCard  FeatureCard             │    │
│  └────────────────────────────────────────────────────────────────────┘    │
│                                                                            │
│  [ ContractDataViz ]                                                       │
│                                                                            │
│  ARTIFACTS (--surface bg, full-width section)                              │
│  OutputFileCard × 4                                                        │
│                                                                            │
│  CTA (full-bleed, 80px 0 100px padding)                                   │
│  HeroInstallBlock (repeated, with section title above)                     │
│                                                                            │
│  FOOTER (border-top, 32px 0)                                              │
└────────────────────────────────────────────────────────────────────────────┘
```

Max-width: 960px centered. Single-column scroll. Anchor links per section.

---

### Layout 2: Installation (Dedicated Section / Page)

Accessible via `#install` anchor from hero CTA or nav. Can also be a standalone `/install` page.

```
┌──────────────────────────────────────────────────────────────────────────┐
│  NAV                                                                     │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  INSTALL                                         (section header)        │
│  ─────────────────────────────                                           │
│  Three steps.                                    (section-title)         │
│  Under 60 seconds.                                                       │
│                                                                          │
│  Step 1. Copy the skill                                                  │
│  ┌── HeroInstallBlock ──────────────────────────────────────────────┐    │
│  │  $ cp -r skills/design-lifecycle ~/.claude/skills/          [⧉]  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  Step 2. (Optional) Configure Mobbin MCP                                 │
│  ┌── OptionalConfigCallout ──────────────────────────────────────────┐   │
│  │  ℹ Without Mobbin, Inspiration falls back to WebSearch.           │   │
│  └───────────────────────────────────────────────────────────────────┘   │
│  ┌── HeroInstallBlock ──────────────────────────────────────────────┐    │
│  │  $ claude mcp add mobbin                                   [⧉]  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  Step 3. Verify installation                                             │
│  ┌── HeroInstallBlock ──────────────────────────────────────────────┐    │
│  │  $ ls ~/.claude/skills/design-lifecycle/                   [⧉]  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  Expected output:                                                        │
│  ┌── TerminalBlock ─────────────────────────────────────────────────┐    │
│  │  design-lifecycle.md  skills/  README.md                         │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

Max-width: `80ch` (~720px) — installation is a reading/copy task, narrower column is more actionable.

---

### Layout 3: Invocation (Reference / Docs Section)

```
┌──────────────────────────────────────────────────────────────────────────┐
│  NAV                                                                     │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  INVOKE                                                                  │
│  Run it.                                                                 │
│                                                                          │
│  ┌ 2-column grid ───────────────────────────────────────────────────┐    │
│  │                            │                                     │    │
│  │  Usage                     │  ┌── TerminalBlock ───────────────┐ │    │
│  │  ───────                   │  │  > /design-lifecycle            │ │    │
│  │  text-sm description of    │  │  > /design-lifecycle auth flow  │ │    │
│  │  the focus argument.       │  │  > /project-showcase            │ │    │
│  │                            │  └────────────────────────────────┘ │    │
│  │  [ ArgumentAnnotation ]    │                                     │    │
│  │                            │                                     │    │
│  └────────────────────────────┴─────────────────────────────────────┘    │
│                                                                          │
│  Common invocations                                                      │
│  ┌── HeroInstallBlock (no copy) ────────────────────────────────────┐    │
│  │  /design-lifecycle authentication flow                           │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│  ┌── HeroInstallBlock ──────────────────────────────────────────────┐    │
│  │  /design-lifecycle payment checkout                              │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│  ┌── HeroInstallBlock ──────────────────────────────────────────────┐    │
│  │  /project-showcase                                               │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

---

### Layout 4: Pipeline Monitoring (Live / In-Session View)

This is the in-context view within a Claude Code session or a companion web panel. Content is live-updating.

```
┌──────────────────────────────────────────────────────────────────────────┐
│  NAV  (minimal — product name only; no nav links during active run)      │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌─ PipelineChecklistPanel ───────────────────────────────────────────┐  │
│  │  design-lifecycle  ·  authentication flow                          │  │
│  │  Started 14:22:07  ·  Phase 3 / 5  ·  [⟳ Running]                │  │
│  │                                                                    │  │
│  │  ╔═══════════════════════════════════════════════════════════════╗  │  │
│  │  ║  ✓  Phase 01  Thinking Partner    Complete      00:04        ║  │  │
│  │  ║  ✓  Phase 02  Inspiration         Complete      00:31        ║  │  │
│  │  ║  ⟳  Phase 03  Maker               Running…      00:07  ← ACTIVE ║ │
│  │  ║  ○  Phase 04  Verifier            Pending       —            ║  │  │
│  │  ║  ○  Phase 05  Amplifier           Pending       —            ║  │  │
│  │  ╚═══════════════════════════════════════════════════════════════╝  │  │
│  │                                                                    │  │
│  │  ████████████░░░░░░░░░░░  60%   3 / 5 phases complete             │  │
│  └────────────────────────────────────────────────────────────────────┘  │
│                                                                          │
│  ▼ Phase 03 · Maker — Live output                                        │
│  ┌── PhaseOutputPreview ────────────────────────────────────────────┐    │
│  │  Writing design-tokens.md...                                     │    │
│  │  Writing component-suggestions.md...                             │    │
│  │  Tokens: 34 defined  Components: 12 specified  Layouts: 5        │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

Active phase row: `--surface-up` background, `3px solid --accent` left border, `StatusChip running` with spinning icon.  
Completed rows: `--surface` background, `StatusChip complete` with ✓ icon.  
Upcoming rows: `--bg` background, reduced opacity icon.

---

### Layout 5: Artifact Consumption (Portfolio / Documentation View)

Accessed post-run. The `/project-showcase` skill output or the `showcase/DOCUMENTATION.md` rendered view. Serves both developer audience (needs output) and portfolio evaluator (needs context).

```
┌──────────────────────────────────────────────────────────────────────────┐
│  NAV: design-lifecycle     Overview · Pipeline · Outputs · About   [Install] │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  SUMMARY HEADER                                                          │
│  Run: authentication flow  ·  Completed 14:28:51  ·  00:47 total        │
│                                                                          │
│  ┌ StatusStat row ──────────────────────────────────────────────────┐    │
│  │  5/5 phases   ·  4 files written  ·  6/6 checks passed          │    │
│  └──────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  ARTIFACTS                                                               │
│  ─────────────────────────────────────────────────────────────────────   │
│  ┌── OutputFileCard ──────────────────────────────────────────────────┐  │
│  │  .design/design-tokens.md      │  Visual foundation    │  TOKENS   │  │
│  ├────────────────────────────────────────────────────────────────────┤  │
│  │  ▼ Preview (ArtifactDetailPanel — expanded)                       │  │
│  │  ┌──────────────────────────────────────────────────────────────┐ │  │
│  │  │  # Design Tokens                                             │ │  │
│  │  │  ...                                                         │ │  │
│  │  └──────────────────────────────────────────────────────────────┘ │  │
│  │  [Open in editor]  [Copy path]                                    │  │
│  └────────────────────────────────────────────────────────────────────┘  │
│  ┌── OutputFileCard (collapsed) ──────────────────────────────────────┐  │
│  │  .design/component-suggestions.md  │  Spec  │  SPEC               │  │
│  └────────────────────────────────────────────────────────────────────┘  │
│  [ more cards... ]                                                       │
│                                                                          │
│  PIPELINE REPLAY (for portfolio evaluators)                              │
│  ─────────────────────────────────────────────────────────────────────   │
│  ┌── PipelineChecklistPanel (read-only, all complete) ───────────────┐   │
│  │  ✓ Phase 01  Thinking Partner    Complete      00:04              │   │
│  │  ✓ Phase 02  Inspiration         Complete      00:31              │   │
│  │  ✓ Phase 03  Maker               Complete      00:09              │   │
│  │  ✓ Phase 04  Verifier            Complete      00:02              │   │
│  │  ✓ Phase 05  Amplifier           Complete      00:01              │   │
│  │  ████████████████████████████  100%  5 / 5 phases                │   │
│  └───────────────────────────────────────────────────────────────────┘   │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Interaction Patterns

### Empty State

**When it occurs:** No pipeline run yet; artifact list before first run; project-showcase with no runs recorded.

**Content:** Centered layout. Icon (the relevant agent emoji in a `icon-xl` wrapper, `--surface-up` bg, `--border` border). Heading `label-lg --text`. Body `text-sm --text-mid` (1–2 sentences explaining what belongs here and how to get there). Single CTA: `Button primary md` with imperative copy.

**Illustration approach:** No custom illustrations. The product's emoji glyphs (🧠, ✦, ⚒, ◉, ⚡) serve as the visual anchor for empty states. They are already meaningful within this product's vocabulary.

```
             ⚒
        No design files yet.
   Run /design-lifecycle to generate
    tokens, components, and a brief.

        [Run design-lifecycle]
```

CTA copy follows the Inspiration Summary principle: imperative, action-first. "Run design-lifecycle" not "Get started."

---

### Loading State

**Principle:** Skeleton screens for structured content (artifact lists, pipeline panels). Spinner only for indeterminate, unstructured loading (a single action with unknown completion time).

**Skeleton screens:**
- Match the exact grid/layout of the loaded state. Use `--surface-up` skeleton blocks with a subtle shimmer animation: `background: linear-gradient(90deg, --surface-up 25%, --surface-overlay 50%, --surface-up 75%)` moving right at 1.5s.
- `border-radius` on skeleton blocks matches the component they replace (`radius-lg` for cards, `radius-sm` for inline labels).
- Minimum skeleton delay: 150ms (do not flash skeleton for fast loads — use `opacity` transition with 150ms delay).
- Maximum content width for text skeletons: 80% of container (avoids full-width skeleton text looking artificial).

**Spinner:**
- 20px, `--accent` stroke, `--surface` fill, 60fps CSS animation.
- Only for: the running phase icon in `StatusChip`, the overall pipeline status indicator, and the clipboard copy action.

**Progressive loading (pipeline):** Show the panel immediately with `pending` rows, then update each row to `running`/`complete` as phase events arrive. Do not wait for all data before rendering the panel.

---

### Error State

**Inline errors** (field validation, single-item failure):
- Red border on the offending input: `1px solid var(--error)`
- Error message below in `text-xs --error-text`, with ✕ icon prepended
- Never replace the field with an error — show the error adjacent to the field

**Phase failure** (pipeline phase returns error):
- `PhaseStatusRow` updates to `failed` state: `StatusChip error`, `--error-subtle` row background
- Left border changes from `--accent` to `--error`
- `PhaseOutputPreview` opens automatically to show error output
- Recovery action: `Button ghost sm` "Retry phase" appears in the row footer

**Run-level failure** (pipeline aborts):
- `PipelineChecklistPanel` header StatusChip → `error`
- Progress bar fill → `--error` color for the failed portion
- Error summary text below the list: `text-sm --error-text`, concise (what failed, not a stack trace)
- CTA: `Button secondary md` "View error output" + `Button ghost md` "Restart run"

**Toast errors** (clipboard failure, network error):
- `Toast error` variant, 5000ms duration (longer than success — errors need reading time)
- Text explains the failure and the recovery action in one sentence: "Copy failed — select the text manually."

**No modals for errors.** Errors are always inline or toast. Modals for errors block the user from context they need to recover.

---

### Success State

**Copy to clipboard:**
- Icon swaps to ✓ (checkmark) for 1500ms, then reverts
- `Toast success` appears: "Copied to clipboard" — 3000ms, then auto-dismisses
- No animation on the surrounding block itself (the icon swap is sufficient)

**Pipeline phase complete:**
- `StatusChip running` (spinner) → `StatusChip complete` (✓) — icon pops with 120ms ease-out scale animation (1 → 1.15 → 1)
- Row background transitions from `--surface-up` (active) to `--surface` (complete) — 150ms ease-out
- Left border fades from `--accent` to transparent — 200ms
- Elapsed time appears in `code-xs --text-dim`

**Full run complete:**
- `PipelineChecklistPanel` progress bar reaches 100%, `--accent` fill
- Header StatusChip → `complete`
- All five rows show `complete` state sequentially (not simultaneously — respect the actual completion order)
- `ArtifactSection` below reveals with 200ms fade-in as files become available

**Form submit / save:**
- Button enters `disabled` state during submission
- On success: button returns to default, `Toast success` appears
- Do not navigate away or reset form on success unless the task was explicitly destructive (e.g., delete)

---

### Partial Data

**Pipeline run with missing phases:**
- Show available phases as normal, missing phases with a `—` dash in the elapsed time column and a muted `StatusChip` in `info` state: "No data"
- Do not hide rows — presence of the row itself communicates that the phase exists in the system

**Artifact file not yet generated:**
- `OutputFileCard` renders with `--text-dim` file path (instead of `--accent-text`)
- Type tag shows "PENDING" instead of "TOKENS" / "SPEC" etc.
- Row hover does not trigger `ArtifactDetailPanel` — cursor: default

**Verification report with partial checks:**
- Show completed checks as `complete`, failed checks as `failed`, unchecked items as `pending`
- Summary stat shows "4 / 6 checks passed" — never round up or hide failed checks
- `--warning` StatusChip on the overall report card if any checks are incomplete but none have failed
- `--error` if any check explicitly failed

**Network/data degraded:**
- Show stale data with a `OptionalConfigCallout`-style info strip at the top of the section: "Showing cached data from 14:22:07. Live updates paused."
- Do not hide stale content — stale data with a timestamp is better than an empty state
- Refresh button (`Button ghost sm`) in the info strip

---

## Implementation Notes

### CSS Architecture

All components should use the CSS custom properties defined in `design-tokens.md`. Do not hardcode hex values in component styles — always reference the token. This ensures theme consistency and makes future adjustments a single-file change.

Component-level custom properties for variants (following the cascade pattern already established in `prototype.html`):

```css
/* Example: PhaseStatusRow variant via data attribute */
.phase-row { background: var(--bg); border-color: var(--border); }
.phase-row[data-status="active"] {
  background: var(--surface-up);
  border-color: var(--border-accent);
  border-left: 3px solid var(--accent);
}
.phase-row[data-status="complete"] { background: var(--surface); }
```

### Animation Budget

Keep animations minimal and purposeful. The total animation budget for a single user interaction is 300ms. Do not stack animations.

| Action | Duration | Easing |
|--------|----------|--------|
| Phase status icon pop | 120ms | ease-out |
| Row background change | 150ms | ease-out |
| Skeleton shimmer | 1500ms | linear (loop) |
| Toast slide-up | 300ms | ease-out |
| Phase output expand | 200ms | ease-out |
| Copy icon swap | instant (swap) + 1500ms hold | — |
| Hover card lift | 200ms | ease-out |

No decorative animations. Every animation must carry information (state change, confirmation, progress).

### Accessibility

- All `StatusChip` components: `role="status"` or `aria-label` on the icon element. Screen readers must hear the label, not just the color.
- `HeroInstallBlock` copy button: `aria-label="Copy install command to clipboard"`. Update to `aria-label="Copied"` during the 1500ms confirmation window.
- `PipelineChecklistPanel`: `role="list"`, each `PhaseStatusRow` is `role="listitem"`. Live updates use `aria-live="polite"` on the panel.
- Progress bar: `role="progressbar"`, `aria-valuenow`, `aria-valuemin="0"`, `aria-valuemax="5"`.
- Focus order: matches visual reading order (top-to-bottom, left-to-right). No focus traps outside of modals (and this spec has no modals).
- Keyboard: all interactive elements reachable by Tab. Enter and Space activate buttons. Copy action also triggered by Enter on the block.
