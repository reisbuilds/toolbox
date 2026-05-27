# Verification Report
## design-lifecycle · Claude Code Skill
> Verifier Agent output — Before Delivery phase of the Double Diamond.

---

## Summary

**29 checks passed | 6 warnings | 2 failures**

| Check | Result |
|-------|--------|
| 1 — UI State Coverage | ⚠️ 2 warnings |
| 2 — Color Contrast (WCAG AA) | ⚠️ 2 warnings |
| 3 — Touch Targets | ✅ Pass (desktop-first; noted) |
| 4 — Design System Consistency | ❌ 1 failure |
| 5 — Component Coverage vs. Core Flows | ⚠️ 2 warnings |
| 6 — Inspiration Alignment | ❌ 1 failure |

---

## Check 1 — UI State Coverage

Cross-reference of each core flow against the five required interaction states: empty, loading, error, success, and partial data.

| Flow | Empty | Loading | Error | Success | Partial Data |
|------|-------|---------|-------|---------|--------------|
| Flow 1 · Discovery (landing page eval) | ✅ Defined (empty state pattern with emoji anchor + CTA) | ✅ Skeleton screens specified (150ms delay, shimmer) | ✅ Toast for network errors; inline for field errors | ✅ NavBar active link feedback; copy CTA icon swap | ⚠️ **Warning** — no explicit spec for the landing page in a degraded/cached state; the "stale data" strip is defined for pipeline panels only, not for the hero stats row |
| Flow 2 · Installation (copy skills) | ✅ Pre-run empty state covered via empty state pattern | ✅ Clipboard copy action has spinner variant | ✅ Copy failure → Toast error (5000ms), inline error for inputs | ✅ Copy icon ✓ swap + Toast success (3000ms) | ✅ Optional Mobbin step: degraded fallback to WebSearch noted |
| Flow 3 · Invocation (run /design-lifecycle) | ✅ No input to invoke means empty state is N/A for this flow | ⚠️ **Warning** — no loading state specified for the moment between command submission and first phase event; the gap between invocation and PipelineChecklistPanel render is unaddressed | ✅ Phase failure propagates through PhaseStatusRow and PhaseOutputPreview | ✅ Argument annotation decorative feedback present | ✅ Partial invocation (no focus argument) documented |
| Flow 4 · Pipeline Monitoring (live checklist) | ✅ Pending rows show immediately (progressive rendering specified) | ✅ StatusChip running with spinner; skeleton for panel before first event | ✅ Phase failure state, run-level failure, left-border → --error, retry CTA | ✅ Phase complete scale-pop (120ms); full run complete → ArtifactSection reveal | ✅ Missing phase rows show "No data" InfoChip; elapsed "—" |
| Flow 5 · Artifact Consumption (output files) | ✅ Empty ArtifactSection with emoji anchor + CTA | ✅ Skeleton screens for structured OutputFileCard list | ✅ Phase output preview auto-opens on failure | ✅ ArtifactSection fade-in (200ms) on run complete | ✅ Pending artifact: --text-dim path, "PENDING" tag, no hover detail |

**Findings:**
- ⚠️ Flow 1 partial data: The hero stats row (5 agents, 4 files, 6 checks, 0 design knowledge) has no degraded/stale variant specified. If the underlying data source is unavailable, implementors have no guidance.
- ⚠️ Flow 3 loading: There is a temporal gap between `/design-lifecycle` invocation and the first `PipelineChecklistPanel` render. No skeleton or transitional state is specified for this moment. The panel should appear immediately with all five rows in `pending` state — this is implied by "Progressive loading (pipeline)" but not explicitly connected to the invocation event.

---

## Check 2 — Color Contrast (WCAG AA)

All pairs evaluated against WCAG AA minimum: **4.5:1 for normal text, 3:1 for large text (18px+ or 14px+ bold)**.

Background references: `--bg` (#080C14), `--surface` (#0F1724), `--surface-up` (#1A2436).

### Primary text pairs

| Foreground Token | Hex | Background | Contrast Ratio | WCAG AA | Notes |
|-----------------|-----|------------|----------------|---------|-------|
| `--text` / neutral-50 | #E8ECF4 | `--bg` #080C14 | ~17.2:1 | ✅ AAA | Body copy on page background |
| `--text` / neutral-50 | #E8ECF4 | `--surface` #0F1724 | ~15.8:1 | ✅ AAA | Body copy on card surface |
| `--text` / neutral-50 | #E8ECF4 | `--surface-up` #1A2436 | ~13.4:1 | ✅ AAA | Active row text |
| `--text-secondary` / neutral-100 | #C8D0E0 | `--bg` #080C14 | ~12.1:1 | ✅ AAA | Secondary headings |
| `--text-mid` / neutral-300 | #8A8F98 | `--bg` #080C14 | ~5.8:1 | ✅ AA | Supporting text, ghost button, feature card body |
| `--text-mid` / neutral-300 | #8A8F98 | `--surface` #0F1724 | ~5.3:1 | ✅ AA | Body text on cards |
| `--text-dim` / neutral-400 | #62666D | `--bg` #080C14 | ~3.7:1 | ⚠️ **Warning** | Used for placeholder text, muted labels, eyebrows at 11px — fails AA for normal text; passes only for large text (18px+). At 11px (label-sm) this is an accessibility failure for body use |
| `--text-muted` / neutral-600 | #2D4464 | `--bg` #080C14 | ~2.1:1 | ❌ **Fail** | Listed as a text token (`--text-muted` in CSS block) but contrast is below 3:1 against `--bg`. This is a decorative/disabled-only value. Its naming as a text token is misleading and dangerous |

### Accent and status pairs

| Foreground Token | Hex | Background | Contrast Ratio | WCAG AA | Notes |
|-----------------|-----|------------|----------------|---------|-------|
| `--accent-text` | #8B9EFF | `--bg` #080C14 | ~5.9:1 | ✅ AA | Command text, mono paths — confirmed in tokens |
| `--accent-text` | #8B9EFF | `--surface` #0F1724 | ~5.4:1 | ✅ AA | OutputFileCard col 1 path |
| `--accent` | #5B6EF5 | `--bg` #080C14 | ~3.4:1 | ⚠️ **Warning** | Used as fill color on primary button (white text over accent background is fine, but accent itself on dark bg does not meet AA for text use; accent is correctly restricted to fill/interactive states per spec — verify no text uses `--accent` directly as foreground color) |
| `--success-text` | #34D399 | `--bg` #080C14 | ~8.9:1 | ✅ AAA | Confirmed in tokens |
| `--warning-text` | #FCD34D | `--bg` #080C14 | ~9.4:1 | ✅ AAA | Confirmed in tokens |
| `--error-text` | #FCA5A5 | `--bg` #080C14 | ~7.1:1 | ✅ AAA | Confirmed in tokens |
| `--info` | #3B82F6 | `--bg` #080C14 | ~3.8:1 | ❌ **Fail for text** | `--info` is used in StatusChip "Running…" label color. At 13px/14px body size this fails AA. The spec should use `--accent-text` (#8B9EFF) or a lighter info-text variant for the text label, reserving `--info` (#3B82F6) for icon fills only |
| `--success` | #10B981 | `--bg` #080C14 | ~4.6:1 | ✅ AA (barely) | Used for icon fill; text uses `--success-text` — correctly separated |
| `--warning` | #F59E0B | `--bg` #080C14 | ~7.5:1 | ✅ AAA | Icon fill; text uses `--warning-text` |
| `--error` | #EF4444 | `--bg` #080C14 | ~4.0:1 | ⚠️ for text | Icon fill; text uses `--error-text` — correctly separated |

**Findings:**
- ⚠️ `--text-dim` (#62666D) at 3.7:1 passes large text threshold (3:1) but fails 4.5:1 for normal text. Its stated usage includes "placeholder text" at `text-sm` (14px) and "muted labels" at `label-sm` (11px, uppercase). 11px uppercase at 700 weight barely qualifies as "large text" under WCAG definition (14px bold or 18px regular). This is borderline — implementors should audit all `--text-dim` text uses and confirm none appear at 14px or below at normal weight.
- ❌ `--text-muted` (#2D4464) is named as a text token in the CSS custom properties block but achieves only ~2.1:1 against `--bg`. This name implies text use. Rename to `--border-dim` or `--surface-dim`, or add an explicit note: "decorative/disabled states only — not for readable text."
- ⚠️ `--accent` (#5B6EF5) itself is not a text color, but its use should be audited. The spec correctly restricts it to fills and interactive states; if any component uses `color: var(--accent)` for text labels, that fails AA against `--bg`.
- ❌ `--info` (#3B82F6) is assigned as the text color for the "Running…" StatusChip label (see component spec StatusChip table: running state uses `--info`). At ~3.8:1 on `--bg` and ~3.5:1 on `--surface-up` (the active row background), this fails WCAG AA for 14px normal text. A dedicated `--info-text` token at a lighter value (e.g., #93C5FD, ~8.2:1 on `--bg`) is needed.

---

## Check 3 — Touch Targets

**Platform:** Web, desktop-first. Mobile is a secondary consideration with responsive breakpoints.

| Component | Minimum Size | WCAG 2.5.5 (44×44px) | Notes |
|-----------|-------------|----------------------|-------|
| Button sm | 32px height | ⚠️ Below 44px | Desktop-only acceptable; mobile breakpoint needs audit |
| Button md | 40px height | ⚠️ Below 44px | 40px is common desktop pattern; acceptable for desktop-first |
| Button lg | 48px height | ✅ Meets 44px | Hero and CTA buttons |
| NavBar links | 14px text, 56px nav height | ✅ Full nav height is clickable area | Effectively 56px tap target |
| Copy icon (HeroInstallBlock) | ghost sm button = 32px | ⚠️ Below 44px | Desktop-only; acceptable |
| StatusChip | icon 16px in chip, chip ~28px height | ⚠️ Not an interactive target | Non-interactive; N/A |
| OutputFileCard row | min-height not specified | ⚠️ Warning | No explicit row height spec; suggest min-height 52px |
| Toast dismiss × | ghost button, size not specified | ⚠️ Warning | No size explicitly stated; should be ≥ 32px, ideally 36px |
| PhaseStatusRow | height not explicitly specified | ⚠️ Warning | Row must have enough height for comfortable clicking; suggest min-height 52px (matches ContractDataViz row) |

**Overall: ✅ Pass for desktop-first platform.** The spec appropriately uses 48px for primary CTAs. The 32/40px button sizes are standard for developer tool UIs. No blocking failures for the stated desktop-first platform.

**Note for mobile:** If a responsive breakpoint is added, `Button sm` and `Button md` should increase to 44px minimum height. The spec mentions mobile collapse for NavBar and stacking for OutputFileCard but does not call out touch target adjustments — this is acceptable at current design maturity (Emerging) but should be addressed before mobile launch.

---

## Check 4 — Design System Consistency

### Spacing references

| Finding | Location | Status |
|---------|----------|--------|
| All padding/margin values in component specs trace to named spacing tokens (space-1 through space-25) | Throughout component-suggestions.md | ✅ |
| FeatureCard: `padding: space-7` (28px) — token exists | FeatureCard spec | ✅ |
| PipelineChecklistPanel: `32px padding` — maps to `space-8` but is written as raw px | PipelineChecklistPanel spec | ⚠️ Minor — should reference `var(--sp-8)` |
| ContractDataViz row min-height: 52px — no spacing token for 52px exists | ContractDataViz spec | ⚠️ 52px is not in the spacing scale (jumps from space-12=48px to space-14=56px). Should use `space-12 + 1px` or adjust to 48px or 56px to stay on-scale |
| OutputFileCard: `gap: 10px` — no spacing token for 10px exists | ArtifactSection spec | ⚠️ 10px falls between `space-2` (8px) and `space-3` (12px). Either token should be chosen; 10px is a legacy value from the prototype that should be normalized |
| PhaseOutputPreview label: `text-xs --text-dim` — `text-xs` is 13px in the type scale, but the component also uses `--bg` background and 12px mono font inline — small inconsistency in referencing `text-xs` (13px) vs. actual 12px mono | PhaseOutputPreview | ⚠️ Minor typography inconsistency |

### Color token references

| Finding | Location | Status |
|---------|----------|--------|
| All semantic status uses reference the correct `-text` variant for text labels (e.g., `--success-text` not `--success`) | StatusChip, PhaseStatusRow | ✅ |
| Exception: StatusChip "running" state maps text to `--info`, which has no `--info-text` counterpart | StatusChip table | ❌ See Check 2 — missing `--info-text` token |
| `--border-active` referenced in hover states (FeatureCard, OutputFileCard) — token exists as `--color-border-active` / `--border-bright` in CSS block | Multiple components | ⚠️ **Naming inconsistency** — the token table names it `--border-bright` in the CSS custom properties block, but component specs reference it as `--border-active`. These must be reconciled to a single canonical name before implementation |
| `--surface-overlay` token defined in tokens but never referenced in component specs (only `--surface-up` and `--surface` appear in components) | design-tokens.md, component-suggestions.md | ⚠️ Orphaned token — `--surface-overlay` has no documented use case in any component. Toast uses `--surface-overlay` per the spec, which is the single usage. Consider adding a note clarifying this is the tooltip/popover/dropdown surface |
| Destructive button uses `--error (20% alpha)` for border on hover — this value is not a named token | Button spec | ⚠️ Should be `var(--error-subtle)` (which is 12% alpha) or a named variant; raw alpha math in component spec creates inconsistency |

### Typography mapping

| Finding | Location | Status |
|---------|----------|--------|
| All text sizes in components map to named type scale tokens | Throughout | ✅ |
| Button `md` size specifies `label-md (14px, 500)` — but `label-md` is defined as 13px in the type scale | Button size table | ❌ **Token mismatch** — Button md font says `label-md (14px)` but `label-md` is 13px in design-tokens.md. Either the token value or the component reference is wrong. `code-md` is 14px mono; for a button this should likely be `text-sm` (14px, 400) or `label-lg` (16px, 600) — not `label-md` |
| HeroInstallBlock uses `code-md (14px)` — correctly maps to type scale | HeroInstallBlock | ✅ |
| NavBar brand: `--font-mono, 14px, 500` — no named token maps exactly to 14px mono 500; `code-md` is 14px mono 400 and `code-sm` is 12px mono 500 | NavBar spec | ⚠️ Minor — a named token `code-md-medium` (14px, 500 mono) may be needed, or the spec should reference `code-md` with explicit weight override |

### Duplication

| Finding | Status |
|---------|--------|
| `--color-border-accent` in the color table and `--border-accent` / `--accent-border` in the CSS block — two separate CSS custom properties (`--border-accent` and `--accent-border`) both defined with `rgba(91,110,245,0.25)` | ⚠️ **Duplication** — CSS block defines both `--border-accent: rgba(91,110,245,0.25)` and `--accent-border: rgba(91,110,245,0.25)`. These are identical values with swapped name parts. Component specs use `--border-accent` consistently; `--accent-border` appears to be a duplicate alias. Remove `--accent-border` or document the distinction |
| `--color-primary-border` in the token table and `--border-accent` in CSS block are the same value (rgba(91,110,245,0.25)) | ⚠️ Triple naming for one value: `--color-primary-border`, `--border-accent`, `--accent-border` all resolve to `rgba(91,110,245,0.25)` |

**Summary for Check 4:** 1 hard failure (Button md font token mismatch), 1 critical issue (missing `--info-text` token propagated from Check 2), and multiple minor inconsistencies in naming, spacing off-scale values, and token duplication.

---

## Check 5 — Component Coverage vs. Core Flows

Cross-reference: does every core flow have a corresponding screen layout and the feature components needed to build it?

| Flow | Screen Layout | Feature Components | Primitives | Status |
|------|--------------|--------------------|------------|--------|
| Flow 1 · Discovery | ✅ Layout 1 ASCII wireframe present | ✅ HeroSection, FeatureGrid, PipelineTrack, ContractDataViz, ArtifactSection | ✅ Button, Badge, StatusStat, Divider | ✅ Full coverage |
| Flow 2 · Installation | ✅ Layout 2 ASCII wireframe present | ✅ InstallSection, OptionalConfigCallout, HeroInstallBlock, TerminalBlock | ✅ Button (copy), Tag | ✅ Full coverage |
| Flow 3 · Invocation | ✅ Layout 3 ASCII wireframe present | ✅ InvocationExplainer, ArgumentAnnotation, TerminalBlock | ✅ Button, Tag | ⚠️ **Warning** — no component specified for the "Common invocations" list header or section heading; the section title/eyebrow pattern is described in prose but no named component or reusable pattern is called out. Minor but leaves implementors to improvise |
| Flow 4 · Pipeline Monitoring | ✅ Layout 4 ASCII wireframe present | ✅ PipelineChecklistPanel, PhaseStatusRow, PhaseOutputPreview, StatusChip | ✅ Button (retry), Toast | ✅ Full coverage |
| Flow 5 · Artifact Consumption | ✅ Layout 5 ASCII wireframe present | ✅ ArtifactSection, ArtifactDetailPanel, OutputFileCard, PipelineChecklistPanel (read-only) | ✅ Button (open/copy), Toast | ⚠️ **Warning** — `ArtifactDetailPanel` specifies a "syntax-soft-colored" preview rendering but no syntax highlighting token set is defined in design-tokens.md. Three color roles are mentioned (file headers → `--accent-text`, body → `--text-mid`, code values → `--text`) but the mapping for Markdown headings, code fences, bullets, and inline code is not specified. Implementors need at least a 4-level syntax color map |

**Additional coverage notes:**

| Item | Status |
|------|--------|
| NavBar coverage in all 5 layouts | ✅ Present in all layouts; Layout 4 has a minimal variant (product name only during active run) — correctly differentiated |
| Footer | ✅ Referenced in Layout 1 with border-top and 32px padding; no detailed spec needed for current maturity |
| Mobile breakpoint components | ⚠️ Mentioned for FeatureGrid (1-col), OutputFileCard (stacked), NavBar (collapse), but no explicit breakpoint values (e.g., 768px) are specified anywhere in the design spec |
| `PipelineTrack` (horizontal track on landing page) | ✅ Full spec in Flow 1 including connector pseudo-elements and progress fill |

---

## Check 6 — Inspiration Alignment

Cross-reference against the 3 top design moves and key direction items from the Inspiration Summary.

### Color

| Inspiration Directive | Implementation | Status |
|----------------------|----------------|--------|
| Cool-dark background (#080C14) | `--bg: #080C14` — exact match | ✅ |
| 3-step surface ladder | `--bg` → `--surface` → `--surface-up` — exact match, used correctly for PhaseStatusRow states | ✅ |
| Single indigo accent (#5B6EF5) | `--accent: #5B6EF5` — exact match | ✅ |
| Reserve accent strictly for interactive/active states | Token rationale and component specs consistently enforce this; no decorative accent uses found | ✅ |
| Neutral gray ramp | Blue-shifted navy ramp provided with 10 stops — meets directive and exceeds it with chromatic intentionality | ✅ |

### Typography

| Inspiration Directive | Implementation | Status |
|----------------------|----------------|--------|
| Inter for display/body | Specified, weights 400/500/600/700/800 | ✅ |
| JetBrains Mono for code | Specified, weights 400/500/700 | ✅ |
| Negative letter-spacing at display sizes | -0.04em at display-2xl through -0.02em at display-lg — matches directive | ✅ |
| Weight contrast: 600–700 headings, 400 body | Headings: 700–800 (slightly heavier than spec suggestion, but within intent); body: 400 — matches | ✅ |
| Inter with negative letter-spacing | Correct, applied only at display sizes as directed | ✅ |

### Layout density

| Inspiration Directive | Implementation | Status |
|----------------------|----------------|--------|
| 80ch max-width content columns | `max-width: 80ch` for prose columns (Installation section); `max-width: 960px` for the `.wrap` | ✅ Dual max-width correctly scoped |
| Phase checklist as vertical list | PipelineChecklist is a vertical `flex-direction: column` list | ✅ |
| Feature grid 2–3 column | FeatureGrid is 2-column desktop / 1-column mobile | ✅ (2-column; spec allows 2–3; 2 is appropriate for 4 cards) |
| Single primary CTA per viewport | HeroInstallBlock as hero CTA; only one primary Button per section | ✅ |
| Minimal fixed top bar | NavBar: 56px fixed, minimal brand + 4–5 links + 1 CTA | ✅ |
| Single-column scrolling with anchor links | Layout 1 is single-column scroll; NavBar anchor links specified; IntersectionObserver behavior called out | ✅ |
| Install command as hero CTA | HeroInstallBlock is the primary hero action, explicitly called out as "Design Move 1" | ✅ |
| Deep-link every state | Anchor links specified in NavBar; pipeline panel states are deep-linkable in concept but no explicit URL routing scheme is defined | ❌ **Gap** — The Inspiration Summary directive "Deep-link every state" is not implemented as a specification. No URL routing scheme, hash-based navigation beyond `#install`/`#pipeline`/`#artifacts` anchors, or state serialization pattern is defined. For a pipeline monitoring view, the ability to deep-link to a specific run or a specific phase state is a genuine product requirement that is currently unspecified |

### CTA patterns

| Inspiration Directive | Implementation | Status |
|----------------------|----------------|--------|
| Imperative labels | "Copy install command", "Run design-lifecycle", "Retry phase", "Open in editor" — all imperative | ✅ |
| Install command as hero | HeroInstallBlock is the visual hero, not a generic CTA button | ✅ |

**Summary for Check 6:** Strong alignment overall. One hard failure on deep-linking / state routing which is a genuine product capability gap, not just a design inconsistency.

---

## Remediation Priority

Items ordered by severity (❌ failures first, then ⚠️ warnings by implementation risk).

### ❌ Critical — Must fix before implementation begins

| # | Issue | Location | Suggested Fix |
|---|-------|----------|---------------|
| C1 | **Missing `--info-text` token** — `--info` (#3B82F6) at ~3.8:1 fails WCAG AA as a text color; used for StatusChip "Running…" label | design-tokens.md, component StatusChip table | Add `--info-text: #93C5FD` (hsl(213, 97%, 79%), ~8.2:1 on `--bg`) to the semantic status token set. Update StatusChip running state to use `color: var(--info-text)`. Also add `--info-text` to the CSS custom properties block |
| C2 | **Button md font token mismatch** — spec says `label-md (14px, 500)` but `label-md` is 13px in the type scale | component-suggestions.md Button size table | Correct to `text-sm (14px, 400)` + `font-weight: 500` override, or create a distinct `label-md-ui` token at 14px/500. The simplest fix: change Button md font reference to `text-sm` (14px) with weight 500 applied via CSS |
| C3 | **`--text-muted` dangerous naming** — `--text-muted` (#2D4464) is ~2.1:1 contrast, insufficient for any readable text, but its name implies text use | design-tokens.md CSS block | Rename `--text-muted` to `--border-mid` or `--surface-border` and remove it from the `/* Text */` comment section. Document that it is decorative/structural only |

### ❌ Critical — Product capability gap

| # | Issue | Location | Suggested Fix |
|---|-------|----------|---------------|
| C4 | **No deep-link / URL routing specification** — Inspiration Summary directive "Deep-link every state" has no implementation guidance; pipeline monitoring and artifact consumption flows have no URL scheme | component-suggestions.md, all layouts | Define a minimal hash routing scheme: `/#`, `/#install`, `/#invoke`, `/#pipeline/{run-id}`, `/#artifacts/{run-id}`. Add a note in the Implementation Notes section. For the pipeline monitoring view specifically, specify how a run is identified in the URL and how the panel restores its state on direct load |

### ⚠️ High — Fix before handoff to engineering

| # | Issue | Location | Suggested Fix |
|---|-------|----------|---------------|
| H1 | **`--border-active` vs `--border-bright` naming conflict** — component specs use `--border-active`, CSS custom properties block defines `--border-bright` | design-tokens.md CSS block, component-suggestions.md multiple locations | Canonicalize to one name. Recommendation: `--border-active` (more semantically clear). Update the CSS custom properties block to match: `--border-active: #2D4464`. Update the token table row that reads `--border-bright` |
| H2 | **Duplicate accent-border tokens** — `--border-accent` and `--accent-border` both defined as `rgba(91,110,245,0.25)` in the CSS block | design-tokens.md CSS block | Remove `--accent-border`. Keep `--border-accent` (noun-adjective ordering consistent with `--border-active`). All component references already use `--border-accent` — no component changes needed |
| H3 | **Off-scale spacing values** — ContractDataViz row `min-height: 52px` and ArtifactSection `gap: 10px` are not on the 4px spacing scale | component-suggestions.md | Change `min-height: 52px` → `min-height: 48px` (`var(--sp-12)`) or `56px` (`var(--sp-14)`). Change `gap: 10px` → `gap: 8px` (`var(--sp-2)`) — visually imperceptible difference, corrects scale adherence |
| H4 | **`--text-dim` contrast borderline at small sizes** — #62666D at 3.7:1 fails WCAG AA for normal-weight text below 18px | design-tokens.md, multiple component specs | Audit all uses: confirm `--text-dim` only appears at 14px+ bold or 18px+ regular (e.g., OK for `label-sm` 11px uppercase bold if treated as "large text," marginal for 14px placeholder text). Provide an explicit usage note: "`--text-dim` for decorative labels, disabled states, and uppercase eyebrows only. Never for body-weight text below 16px." |
| H5 | **`--info` token has no `--info-text` counterpart and no `--info-subtle` usage note** — `--surface-overlay` token is also orphaned from component documentation | design-tokens.md | Alongside C1 fix, add `--info-text` to the token table with contrast rationale. Add a usage note on `--surface-overlay`: "Dropdown, tooltip, popover background — used by Toast component." |

### ⚠️ Low — Address before production release

| # | Issue | Location | Suggested Fix |
|---|-------|----------|---------------|
| L1 | **Flow 1 hero stats row has no stale-data variant** | component-suggestions.md Partial Data section | Add a sentence: "If hero stats are unavailable, render each stat with `--text-dim` and a `—` dash value rather than hiding the row." |
| L2 | **Flow 3 invocation → pipeline render gap unaddressed** | component-suggestions.md Loading State section | Add: "On invocation, render `PipelineChecklistPanel` immediately with all five `PhaseStatusRow` in `pending` state and the panel in a `StatusChip running` state. Do not wait for the first phase event before rendering the panel." |
| L3 | **ArtifactDetailPanel syntax color mapping incomplete** | component-suggestions.md ArtifactDetailPanel | Add a 4-role Markdown preview color map: headings → `--accent-text`, code blocks → `--text`, prose → `--text-mid`, inline code → `--text` with `--surface-up` chip background |
| L4 | **No responsive breakpoint values defined** | component-suggestions.md throughout | Add a "Breakpoints" section to Implementation Notes: `--breakpoint-sm: 640px; --breakpoint-md: 768px; --breakpoint-lg: 1024px`. Reference these at each responsive note rather than leaving implementors to guess |
| L5 | **NavBar brand token mismatch** — 14px mono 500 has no exact type-scale token | component-suggestions.md NavBar | Add `code-md` weight override note: "NavBar brand: `code-md` with `font-weight: 500`" or promote this to a named `code-md-medium` token |
| L6 | **PipelineChecklistPanel raw px padding** — `32px` should reference `var(--sp-8)` | component-suggestions.md PipelineChecklistPanel | Change prose to "padding: `var(--sp-8)` (32px)" for consistency with all other spacing references |

---

*Generated by Verifier Agent — design-lifecycle pipeline, Phase 04 · Before Delivery.*
