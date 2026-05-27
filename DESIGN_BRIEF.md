# Design Brief — Toolbox Design Lifecycle Skill

> Build a developer tool UI that earns trust through precision and restraint, enabling a time-pressured developer to evaluate, install, and run the skill in a single scroll — while simultaneously serving portfolio evaluators who need to understand what was built and why.

---

## App Context

**Genre:** Developer tool / CLI / technical  
**Platform:** Web (desktop-first)  
**Design Maturity:** Emerging — hand-crafted prototype HTML with a coherent dark-mode token system; not yet a formal design system  
**Core Flows:**
1. **Discovery** — landing page evaluation; developer assesses fit and credibility
2. **Installation** — copying skills into `~/.claude/skills/` via a one-command install block
3. **Invocation** — running `/design-lifecycle` or `/project-showcase` with arguments
4. **Pipeline Monitoring** — watching phase checklist with live status indicators
5. **Artifact Consumption** — reading and acting on generated output files

---

## Design Philosophy

Precision is the primary aesthetic signal. Every dimension, color, and spacing value communicates that this tool was built by someone who cares about correctness — and that signal extends to trust in the generated output. The UI uses restraint as a feature: color appears only when it carries meaning, space is a design element, and typographic weight does the heavy lifting that decoration would otherwise perform.

The interface serves two simultaneous audiences without compromise. The developer who needs the tool now gets a single dominant CTA, zero decorative friction, and scannable information hierarchy. The portfolio evaluator who needs to understand intent gets rationale woven into the page structure — the pipeline track and artifact section double as documentation.

Technical rigor is communicated through exactness: monospace code blocks for all commands, tabular numerals for data values, hairline borders instead of shadows for depth, and status indicators that always pair color with icon and label — never color alone.

---

## Inspiration References

**Linear (linear.app)**  
Near-black surface ladder communicates application depth without heavy shadows. Negative letter-spacing at display sizes (`-0.04em` at 80px) signals engineering taste. Single restrained accent color (here, indigo) replaces decorative multi-color palettes.

**Raycast (raycast.com)**  
Monochrome dark canvas with the command palette as hero establishes product identity immediately. Hairline `1px` borders define component edges. A single white pill CTA per viewport eliminates decision paralysis. Negative tracking on headings carried forward directly.

**Vercel / Geist (vercel.com/geist)**  
Pure-neutral gray ramp with color reserved for meaning, not decoration. `tabular-nums` font feature for all numeric data (stats, progress counts, timestamps). Action-oriented imperative CTA copy: "Install", "Run", "View output" — not "Get started" or "Learn more".

**Carbon Design System (carbondesignsystem.com)**  
Status indicator pattern: every status state uses color + icon + text label. Never color alone. Applied directly to the PipelineChecklist phase states (pending, active, complete, error).

**Homebrew (brew.sh)**  
The install command is the hero — a monospace bordered block with copy-to-clipboard is the primary CTA above the fold. This pattern is the single most important layout decision in the install section.

---

## Design Tokens

### Color

```css
:root {
  /* Accent */
  --accent:          #5B6EF5;
  --accent-text:     #8B9EFF;
  --accent-dark:     #3A4FD4;
  --accent-border:   /* REMOVED — duplicate of --border-accent */

  /* Background / Surface ladder */
  --bg:              #080C14;
  --surface:         #0F1724;
  --surface-up:      #1A2436;
  --surface-overlay: #1E293B;

  /* Borders */
  --border:          #1E2D44;
  --border-active:   #2D4464;  /* canonical — replaces any --border-bright usage */
  --border-accent:   rgba(91, 110, 245, 0.25);

  /* Neutral text ramp */
  --neutral-50:      #E8ECF4;  /* primary text */
  --neutral-100:     #D1D8E8;
  --neutral-200:     #B0BCDA;
  --neutral-300:     #8A99C0;
  --neutral-400:     #6677A6;
  --neutral-500:     #4D5D8C;
  --neutral-600:     #3A4870;
  --neutral-700:     #2B3659;
  --neutral-800:     #1E2745;
  --neutral-900:     #080C14;  /* matches --bg */

  /* Semantic text */
  --text:            #E8ECF4;  /* neutral-50 */
  --text-secondary:  #B0BCDA;  /* neutral-200 */
  --text-dim:        #6677A6;  /* neutral-400 — restrict to uppercase/bold labels only; 3.7:1 contrast */
  /* NOTE: --text-muted (#2D4464) REMOVED — 2.1:1 contrast, not safe as text; use --border-active for border use cases */

  /* Status */
  --success:         #10B981;
  --success-text:    #34D399;
  --warning:         #F59E0B;
  --warning-text:    #FCD34D;
  --error:           #EF4444;
  --error-text:      #FCA5A5;
  --info:            #3B82F6;
  --info-text:       #93C5FD;  /* ADDED — required for WCAG AA text on dark bg */
}
```

**Contrast notes:**
- `--accent-text` (#8B9EFF on #080C14): 7.2:1 — AA/AAA pass
- `--text` (#E8ECF4 on #080C14): 15.4:1 — AAA pass
- `--text-secondary` (#B0BCDA on #0F1724): 8.1:1 — AAA pass
- `--text-dim` (#6677A6 on #0F1724): 3.7:1 — AA pass for large/bold text only; use exclusively for uppercase labels ≥11px/700 or decorative context
- `--info` (#3B82F6 on #080C14): 3.8:1 — fails AA as body text; use `--info-text` (#93C5FD) for all info-state text

### Typography

```css
/* Font families */
--font-sans: 'Inter', system-ui, -apple-system, sans-serif;
--font-mono: 'JetBrains Mono', 'Fira Code', monospace;

/* Font feature settings */
--feature-display: 'cv01', 'cv03', 'cv04';  /* Inter optical alternates */
--feature-tabular: 'tnum';  /* for all numeric data */

/* Display scale — used for hero and section headings */
--text-display-xl: 80px;  line-height: 1.0;  letter-spacing: -0.04em;  font-weight: 800;
--text-display-lg: 56px;  line-height: 1.05; letter-spacing: -0.03em;  font-weight: 700;
--text-display-md: 40px;  line-height: 1.1;  letter-spacing: -0.025em; font-weight: 700;

/* Heading scale */
--text-heading-xl: 32px;  line-height: 1.2;  letter-spacing: -0.02em;  font-weight: 700;
--text-heading-lg: 24px;  line-height: 1.25; letter-spacing: -0.015em; font-weight: 600;
--text-heading-md: 20px;  line-height: 1.3;  letter-spacing: -0.01em;  font-weight: 600;
--text-heading-sm: 18px;  line-height: 1.35; letter-spacing: -0.01em;  font-weight: 600;

/* Body scale */
--text-body-lg:  16px;  line-height: 1.6;  font-weight: 400;
--text-body-md:  14px;  line-height: 1.6;  font-weight: 400;  /* default body */
--text-body-sm:  13px;  line-height: 1.55; font-weight: 400;

/* Label scale — uppercase tracking for UI labels */
--text-label-lg: 16px;  line-height: 1.4;  font-weight: 500;
--text-label-md: 14px;  line-height: 1.4;  font-weight: 500;  /* button text size — see C2 note */
--text-label-sm: 11px;  line-height: 1.3;  font-weight: 600;  letter-spacing: 0.06em; text-transform: uppercase;

/* Code scale — JetBrains Mono */
--text-code-lg:  14px;  line-height: 1.6;
--text-code-md:  12px;  line-height: 1.5;
--text-code-sm:  10px;  line-height: 1.4;
```

**Button font-size fix (C2):** Button medium uses `--text-label-md` at **14px** (not 13px). The type scale token `--text-label-md` is corrected to 14px. This is a one-token fix.

### Spacing

4px base grid. All spacing values are multiples of 4.

```css
--space-1:   4px;
--space-2:   8px;
--space-3:   12px;
--space-4:   16px;
--space-5:   20px;
--space-6:   24px;
--space-8:   32px;
--space-10:  40px;
--space-12:  48px;  /* was 52px — corrected to grid */
--space-16:  64px;
--space-20:  80px;
--space-25:  100px;

/* Off-grid values removed: 52px → 48px, 10px → 8px */
```

### Border Radius

```css
--radius-xs:   4px;
--radius-sm:   6px;
--radius-md:   8px;
--radius-lg:   12px;
--radius-xl:   16px;
--radius-full:  9999px;
```

### Elevation / Shadows

```css
--shadow-sm:     0 1px 2px rgba(0, 0, 0, 0.4);
--shadow-md:     0 4px 12px rgba(0, 0, 0, 0.5);
--shadow-lg:     0 12px 32px rgba(0, 0, 0, 0.6);
--shadow-accent: 0 0 0 4px rgba(91, 110, 245, 0.15);  /* focus ring */
--shadow-glow:   0 0 24px rgba(91, 110, 245, 0.2);
```

### URL Routing

Hash routing scheme for deep-linking pipeline state:

```
/#pipeline/{run-id}
/#pipeline/{run-id}/phase/{phase-slug}
/#artifacts/{run-id}
```

The pipeline panel reads the URL hash on mount and scrolls to the active phase. Each phase row has an `id` matching `phase-{slug}`. This enables sharing a live pipeline URL.

---

## Component Specification

### Primitives

**Button**

| Variant     | Background        | Text              | Border            |
|-------------|-------------------|-------------------|-------------------|
| primary     | `--accent`        | `#FFFFFF`         | none              |
| secondary   | `--surface-up`    | `--text`          | `--border`        |
| ghost       | transparent       | `--accent-text`   | none              |
| destructive | `--error` (10% bg)| `--error-text`    | `--error` (30%)   |

| Size | Height | H-padding  | Font                 | Radius      |
|------|--------|------------|----------------------|-------------|
| sm   | 28px   | 12px       | `--text-label-sm`    | `--radius-sm` |
| md   | 36px   | 16px       | `--text-label-md` (14px) | `--radius-md` |
| lg   | 44px   | 24px       | `--text-label-lg`    | `--radius-md` |

Focus state: `outline: none; box-shadow: var(--shadow-accent)`.  
Disabled state: `opacity: 0.4; cursor: not-allowed`.

**Badge / StatusChip**

Always renders: `[icon] [label]` — never icon alone, never color alone.

```
StatusChip variants: pending | active | complete | error | warning
- pending:  icon=○  bg=--surface-up   text=--text-dim      border=--border
- active:   icon=◉  bg=--accent(10%)  text=--accent-text   border=--border-accent
- complete: icon=✓  bg=--success(10%) text=--success-text  border=--success(30%)
- error:    icon=✕  bg=--error(10%)   text=--error-text    border=--error(30%)
- warning:  icon=△  bg=--warning(10%) text=--warning-text  border=--warning(30%)
```

Height: 22px, padding: 4px 8px, radius: `--radius-full`, font: `--text-label-sm`.

**Input / Textarea / Select**

Background: `--surface`. Border: `1px solid --border`. Radius: `--radius-md`. Padding: 8px 12px.  
Focus: border-color `--accent`, box-shadow `--shadow-accent`.  
Font: `--text-body-md`. Placeholder: `--text-dim`.

**Icon Wrapper**

| Size    | Dimensions | Icon size |
|---------|------------|-----------|
| xs      | 16×16      | 10px      |
| sm      | 20×20      | 12px      |
| md      | 24×24      | 14px      |
| lg      | 32×32      | 18px      |
| xl      | 40×40      | 24px      |

---

### Molecules

**NavBar**

- Height: 56px fixed, `backdrop-filter: blur(12px)`, background: `rgba(8,12,20,0.85)`
- Border-bottom: `1px solid --border`
- Logo left, nav links center, primary CTA right
- Active link detection: IntersectionObserver watching section `id` anchors
- Active link style: `--accent-text`, no underline, no background
- All links anchor to on-page sections; no page transitions

**HeroInstallBlock**

The primary CTA. Renders the install command as a monospace bordered block.

```
┌─────────────────────────────────────────────────────────┐
│  $ cp -r ./skills/* ~/.claude/skills/                   │  [Copy]
└─────────────────────────────────────────────────────────┘
```

- Background: `--surface`. Border: `1px solid --border-active`. Radius: `--radius-lg`
- Font: `--text-code-lg` (14px). Padding: 16px 20px
- `[Copy]` button: ghost variant, sm size, right-aligned
- On copy: icon swaps to checkmark for 1500ms then reverts
- `$` prefix: `--text-dim`. Command text: `--neutral-50`

**TerminalBlock**

Multi-line output display. Same visual treatment as HeroInstallBlock but taller, with a `[phase label]` header bar at top (`--surface-overlay`, 8px 16px padding, `--text-label-sm`).

**PipelineChecklist**

Vertical list of phase rows. Each row uses one of three elevation states:

| Phase state  | Background     | Left border        | Opacity |
|--------------|----------------|--------------------|---------|
| upcoming     | `--bg`         | `--border`         | 70%     |
| active       | `--surface-up` | `--accent` (2px)   | 100%    |
| complete     | `--surface`    | `--success` (2px)  | 100%    |
| error        | `--surface`    | `--error` (2px)    | 100%    |

Each row: `[StatusChip] [Phase name] [phase-output-count badge]`. Right side: `[timestamp]` in `--text-dim`, `tabular-nums`.

Progress bar above list: `--surface-up` track, `--accent` fill, height 2px, `border-radius: --radius-full`. Value = complete_phases / total_phases.

`aria-live="polite"` on the panel container. Each phase row `id="phase-{slug}"` for deep-linking.

**FeatureCard**

Grid: 2 columns, gap `--space-6`. Cards: `--surface`, border `--border`, radius `--radius-lg`, padding `--space-6`.  
Icon: lg Icon Wrapper at top. Heading: `--text-heading-sm`. Body: `--text-body-sm`, `--text-secondary`.

**OutputFileCard**

3-column layout per card: `[file path] | [description] | [type badge]`.  
File path: `--text-code-md`, `--accent-text`. Description: `--text-body-sm`. Type badge: StatusChip using `--info-text` / `--info`.  
Grid: 3 columns desktop, 1 column mobile.

**StatusStat**

```
[large number]
[label]
```

Number: `--text-display-md` (40px), `tabular-nums`, `--neutral-50`.  
Label: `--text-label-sm`, `--text-dim`, uppercase.  
Stale-data variant: number color shifts to `--warning-text`, tooltip "Last updated [timestamp]".

**Toast**

Bottom-right, `--surface-overlay`, border `--border-active`, radius `--radius-lg`, shadow `--shadow-md`.  
Auto-dismiss: 4000ms (error: no auto-dismiss). Width: 320px max. No stacking — queue sequentially.  
Never use modals for error states; always surface inline or via toast.

---

### Feature Organisms

**HeroSection (Discovery)**

Layout: single column, max-width 80ch, centered.  
Order: eyebrow label → display headline → subhead → HeroInstallBlock → secondary link.  
Eyebrow: `--text-label-sm`, `--accent-text`, uppercase, letter-spacing 0.08em.  
Headline: `--text-display-lg` (56px). Subhead: `--text-body-lg`, `--text-secondary`, max-width 60ch.

**FeatureGrid (Discovery)**

Section: max-width 1100px, centered. Heading: `--text-heading-xl`. Grid: 2-col FeatureCards.

**PipelineTrack (Discovery / Pipeline Monitoring)**

Full-width section with `--surface` background. Shows pipeline phases as horizontal track on desktop, vertical list on mobile. Each phase node uses the 3-elevation system. Clicking a phase deep-links to `/#pipeline/{run-id}/phase/{phase-slug}`.

**InstallSection (Installation)**

HeroInstallBlock as primary element. Below it: `OptionalConfigCallout` — `--surface-up` background, `--border-accent` border, radius `--radius-md`, padding `--space-5`. Contains optional `~/.claude/CLAUDE.md` snippet for project-scope config.

**InvocationExplainer (Invocation)**

Two-column layout: left column command block (`TerminalBlock`), right column `ArgumentAnnotation` — a vertical list of arg name + description pairs. Arg name: `--text-code-md`, `--accent-text`. Description: `--text-body-sm`.

**PipelineChecklistPanel (Pipeline Monitoring)**

Full-width panel. Header: run ID + elapsed time (`tabular-nums`) + overall StatusChip.  
Progress bar. Phase list (PipelineChecklist). Below active phase: `PhaseOutputPreview` — a collapsed TerminalBlock showing last 5 lines of phase output, expandable.  
URL hash: `/#pipeline/{run-id}`. On mount: read hash, scroll to active phase row.  
Invocation-to-pipeline render gap: show skeleton screens (150ms delay before appearing) for phase rows while initial pipeline data loads. Skeleton uses `--surface-up` animated shimmer.

**ArtifactSection (Artifact Consumption)**

Grid of OutputFileCards. Clicking a card opens `ArtifactDetailPanel` inline (not modal) — slides down below the card row, 300ms ease-out. Panel contains: file metadata header, Markdown preview area, close button.

**ArtifactDetailPanel Markdown color map:**

| Markdown element | Token                | Font                  |
|-----------------|----------------------|-----------------------|
| h1–h2           | `--text`             | `--text-heading-lg/md` |
| h3–h6           | `--text-secondary`   | `--text-heading-sm`   |
| body text       | `--text-secondary`   | `--text-body-md`      |
| code inline     | `--accent-text`      | `--text-code-md`      |
| code block      | `--neutral-50`       | `--text-code-md`      |
| blockquote      | `--text-dim`         | `--text-body-md`      |
| links           | `--accent-text`      | underline on hover    |
| strong          | `--text`             | font-weight: 600      |

---

### Interaction Patterns

**Phase status transitions**

Pending → Active: StatusChip swaps icon, left border color transitions, background shifts to `--surface-up`. Duration: 120ms ease-out.  
Active → Complete: icon animates scale 1.0 → 1.2 → 1.0 (scale-pop). Duration: 150ms. Background shifts to `--surface`.

**Skeleton screens**

Appear after 150ms delay (prevents flash on fast loads). Shape: `--surface-up` rectangles matching content dimensions, `border-radius: --radius-sm`. Shimmer: `background-position` animation, 1.5s infinite.

**Copy checkmark swap**

On clipboard write: icon changes from copy icon to checkmark icon. Reverts after 1500ms. No animation — instant swap.

**Empty states**

Use product-relevant emoji glyphs (e.g., 🔧 for no artifacts, ⚙️ for no pipeline runs). Single emoji, short label, no illustrations.

**Error states**

Never modal. Use: inline validation below inputs, StatusChip on affected row, Toast for async errors. Error text always uses `--error-text`, not `--error` (contrast).

---

## Verification Status

| # | Category | Check | Status |
|---|----------|-------|--------|
| C1 | Color | `--info-text` token defined (#93C5FD) | Fixed in this brief |
| C2 | Typography | Button md font-size = 14px | Fixed in this brief |
| C3 | Color | `--text-muted` removed; renamed to `--border-active` | Fixed in this brief |
| C4 | Routing | Deep-link URL scheme defined | Fixed in this brief |
| H1 | Naming | `--border-active` canonical (remove `--border-bright`) | Fixed in this brief |
| H2 | Naming | `--border-accent` canonical (remove `--accent-border`) | Fixed in this brief |
| H3 | Spacing | 52px → 48px, 10px → 8px | Fixed in this brief |
| H4 | Color | `--text-dim` restricted to uppercase/bold labels | Documented above |
| L1 | Component | Stale-data variant for StatusStat | Documented above |
| L2 | Interaction | Invocation-to-pipeline render gap | Documented above |
| L3 | Component | ArtifactDetailPanel Markdown color map | Documented above |
| L4 | Layout | Responsive breakpoints | See Next Steps |

**Open Items:**

- **L4 (open):** Responsive breakpoints not yet defined. Identified breakpoints needed: 1280px (wide desktop), 1024px (desktop), 768px (tablet), 480px (mobile). Specific token values and layout shifts require a dedicated responsive pass — see Next Steps.

---

## Design Decisions & Rationale

**1. Single indigo accent, used only for interactive and active states**  
Why: Multiple accent colors fragment attention. One accent color trains the eye — anything indigo is interactive or currently active.  
Tradeoff: Information density is lower at a glance. Compensated by explicit status labels and icon shapes on all status indicators.

**2. Install command as the primary hero CTA**  
Why: Developers evaluate a tool by its install story. Putting the command above the fold eliminates the most critical conversion friction. The copy-to-clipboard interaction removes the last step before trial.  
Tradeoff: Non-developer visitors (portfolio evaluators) see a code block first. Accepted tradeoff — the target audience for conversion is developers, and portfolio evaluators are served by the page's overall architecture.

**3. Three-step surface ladder for pipeline phase elevation**  
Why: The pipeline is the product's core UI. The elevation system — `--bg` for upcoming, `--surface-up` for active, `--surface` for complete — creates an instant spatial reading of progress without any color-coded labels.  
Tradeoff: The three grays are close in value; the distinction requires careful implementation on calibrated displays. The left border color reinforces the elevation distinction as a second signal.

**4. Status indicators always use color + icon + label**  
Why: Color-blind accessibility, but also clarity for everyone. A spinner communicates "loading" even in grayscale. A checkmark communicates "complete" on a monochrome printout.  
Tradeoff: More horizontal space per status chip than a color dot alone. Compensated by the compact 22px chip height.

**5. No modals for errors**  
Why: Modals interrupt workflow and create a dismissal step between the developer and the next action. Error states appear inline or as non-blocking toast. This matches the mental model of terminal output — errors appear in context, not in a popup.  
Tradeoff: Inline errors require careful layout planning so they don't shift content positions. The 150ms skeleton delay prevents the most common layout shift trigger.

**6. `tabular-nums` for all numeric data**  
Why: Elapsed times, phase counts, and stat numbers change as the pipeline runs. Without tabular numerals, number widths change and elements shift on update. `font-feature-settings: 'tnum'` prevents reflow from numeric updates.  
Tradeoff: Zero visual cost, minor implementation step. No tradeoff.

**7. 80ch max-width prose columns**  
Why: 80 characters per line is the developer-familiar line length from terminal and code editors. It signals the correct register for a developer tool and optimizes line length for reading speed.  
Tradeoff: Wide desktop viewports have significant horizontal whitespace at this column width. This is a feature — it prevents the layout from feeling like marketing sprawl.

---

## Next Steps

**For Designers:**
- [ ] Define responsive breakpoints (L4): specify exact px values for 4 breakpoints and document layout changes at each (nav collapse, grid column reduction, HeroInstallBlock horizontal/vertical stack)
- [ ] Audit all 3-step surface colors on a calibrated display — confirm `--bg` / `--surface` / `--surface-up` are perceptually distinct at 100% brightness
- [ ] Produce motion spec: define easing curves (ease-out cubic-bezier values) for phase transitions, ArtifactDetailPanel slide, skeleton shimmer
- [ ] Design the empty state illustrations / emoji compositions for each flow section
- [ ] Specify the PhaseOutputPreview expand/collapse interaction (height animation or fade?)
- [ ] Document the NavBar mobile collapse — hamburger pattern or bottom drawer?

**For Developers:**
- [ ] Implement `--info-text: #93C5FD` CSS variable (was missing from prototype)
- [ ] Fix button medium font-size to 14px (`--text-label-md`)
- [ ] Remove `--text-muted`, `--accent-border`, `--border-bright` — replace all usages with canonical tokens
- [ ] Correct spacing values: replace any 52px with `--space-12` (48px), replace any 10px with `--space-2` (8px)
- [ ] Add `id="phase-{slug}"` to all phase rows; implement hash routing on PipelineChecklistPanel mount
- [ ] Add `aria-live="polite"` to PipelineChecklistPanel container
- [ ] Implement 150ms skeleton delay before showing skeleton screens (use `setTimeout` guard)
- [ ] Apply `font-feature-settings: 'tnum'` to all numeric display elements
- [ ] Scope `--text-dim` usage: audit codebase and restrict to uppercase/bold labels only

**For the Team:**
- [ ] Decide: does the portfolio evaluator audience need a dedicated "About this build" section, or does the artifact output serve that purpose?
- [ ] Define stale-data threshold for StatusStat — how old before showing the warning state?
- [ ] Specify run ID format — UUID, timestamp-slug, or sequential? Required for deep-link URL scheme
- [ ] Confirm whether IntersectionObserver active-link detection needs a fallback for no-JS environments
- [ ] Review responsive breakpoint pass output before any mobile implementation begins
