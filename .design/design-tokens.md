# Design Tokens
## design-lifecycle · Claude Code Skill
> Extends the values established in `showcase/prototype.html`. Do not start over — extend.

---

## Color Palette

### Brand / Action Color

| Token | Hex | HSL | CSS Custom Property |
|-------|-----|-----|---------------------|
| `--color-primary` | `#5B6EF5` | `hsl(234, 88%, 65%)` | `--accent` (existing) |
| `--color-primary-light` | `#8B9EFF` | `hsl(233, 100%, 77%)` | `--accent-text` (existing) |
| `--color-primary-subtle` | `rgba(91,110,245,0.12)` | — | `--accent-glow` variant |
| `--color-primary-border` | `rgba(91,110,245,0.25)` | — | used on focus rings, accent card borders |
| `--color-primary-dark` | `#3A4FD4` | `hsl(233, 66%, 52%)` | pressed states, active nav items |

**Primary rationale:** `#5B6EF5` is the established prototype accent — a cool indigo sitting precisely between Linear's `#5e6ad2` and a pure blue. It reads as technical and intentional without tipping into "tech startup purple." Reserve strictly for interactive and active states.

**Primary Light rationale:** `#8B9EFF` is the existing `--accent-text`. It passes 4.5:1 on `--bg` (#080C14) for text-sized use. Use for monospace command output, active labels, and hover indicator text — never as a fill.

**Primary Subtle rationale:** 12% opacity fill produces the pill-receives badge and the hero eyebrow background. Keeps the accent visible without the surface reading as "colored."

**Primary Dark rationale:** `#3A4FD4` is the pressed/active variant at ~17% darker. Use on `:active` button states and the active nav link underline.

---

### Neutral Ramp

| Token | Hex | Role |
|-------|-----|------|
| `--neutral-50` | `#E8ECF4` | Primary text on dark — high contrast body copy |
| `--neutral-100` | `#C8D0E0` | Secondary headings, strong labels |
| `--neutral-200` | `#A8B2C8` | Supporting text, non-critical metadata |
| `--neutral-300` | `#8A8F98` | Secondary body, placeholder text |
| `--neutral-400` | `#62666D` | Muted labels, disabled text, eyebrows |
| `--neutral-500` | `#4A5568` | Hairline dividers visible on surface |
| `--neutral-600` | `#2D4464` | Bright borders, hover state borders |
| `--neutral-700` | `#1E2D44` | Default border — `--border` (existing) |
| `--neutral-800` | `#0F1724` | Card surface — `--surface` (existing) |
| `--neutral-900` | `#080C14` | Page background — `--bg` (existing) |

**Rationale:** This is not a symmetric gray ramp. It is a blue-shifted navy ramp, matching the existing prototype's chromatic undertone. The cool shift at every stop ensures the dark theme reads as purposeful (developer tool) rather than generic charcoal. Neutral-50 through 400 are for text; 500–700 for borders and hairlines; 800–900 for surfaces.

---

### Surface Colors

| Token | Hex | CSS Custom Property | Rationale |
|-------|-----|---------------------|-----------|
| `--color-bg` | `#080C14` | `--bg` | Deepest layer — page background, nav backdrop base |
| `--color-surface` | `#0F1724` | `--surface` | Default card and panel surface |
| `--color-surface-raised` | `#1A2436` | `--surface-up` | Elevated interactive card, active/focused panel |
| `--color-surface-overlay` | `#1E293B` | — | Dropdown, tooltip, popover background |
| `--color-border` | `#1E2D44` | `--border` | Resting hairline border |
| `--color-border-active` | `#2D4464` | `--border-bright` | Hover / focused border |
| `--color-border-accent` | `rgba(91,110,245,0.25)` | — | Accent-tinted border on focused inputs, active phase |

**3-step surface ladder:** `--bg` (base) → `--surface` (card) → `--surface-up` (elevated/active).  
The design move from the Inspiration Summary: active pipeline phase sits at `--surface-up`, completed phases at `--surface`, upcoming phases at `--bg`. Depth without color.

---

### Semantic / Status Colors

These are reserved exclusively for pipeline status indicators and destructive actions. Never used for decoration.

| Token | Hex | HSL | Usage |
|-------|-----|-----|-------|
| `--color-success` | `#10B981` | `hsl(160, 84%, 39%)` | Phase complete checkmark, passing verification |
| `--color-success-subtle` | `rgba(16,185,129,0.12)` | — | Success icon background fill |
| `--color-success-text` | `#34D399` | `hsl(161, 72%, 53%)` | Success text labels on dark surface |
| `--color-warning` | `#F59E0B` | `hsl(37, 91%, 50%)` | Phase paused / in-progress with risk |
| `--color-warning-subtle` | `rgba(245,158,11,0.12)` | — | Warning icon background fill |
| `--color-warning-text` | `#FCD34D` | `hsl(44, 96%, 65%)` | Warning text on dark surface |
| `--color-error` | `#EF4444` | `hsl(0, 84%, 60%)` | Phase failed, error state |
| `--color-error-subtle` | `rgba(239,68,68,0.12)` | — | Error icon background fill |
| `--color-error-text` | `#FCA5A5` | `hsl(0, 93%, 82%)` | Error text on dark surface |
| `--color-info` | `#3B82F6` | `hsl(217, 91%, 60%)` | Informational callout, neutral phase status |
| `--color-info-subtle` | `rgba(59,130,246,0.12)` | — | Info icon background fill |

**Carbon Design System principle applied:** Status is always communicated with icon + color + label. Never color alone. The `--color-success` green is never used as an accent or highlight; it is reserved for "this thing succeeded."

**Contrast check (against `--bg` #080C14):**
- `#34D399` success text: ~8.9:1 — passes AAA
- `#FCD34D` warning text: ~9.4:1 — passes AAA
- `#FCA5A5` error text: ~7.1:1 — passes AAA
- `#8B9EFF` accent text: ~5.9:1 — passes AA

---

## Typography

### Font Families

| Role | Family | Source | Weights to Load |
|------|--------|--------|-----------------|
| **Display / Heading** | Inter | Google Fonts | 600, 700, 800 |
| **Body / UI** | Inter | Google Fonts | 400, 500 |
| **Mono** | JetBrains Mono | Google Fonts | 400, 500, 700 |

**Inter rationale:** Already loaded in the prototype. Inter at weights 700–800 with negative letter-spacing (`-0.02em` to `-0.04em`) reads as precise and restrained — matching Linear, Raycast, and Vercel's density at display sizes. Load only what is used: 400 body, 500 label/UI, 600–800 heading.

**JetBrains Mono rationale:** All command strings (`/design-lifecycle`), file paths (`.design/design-tokens.md`), install snippets, phase output labels, and contract pill labels use JetBrains Mono. The ligatures are acceptable for code context; if they introduce ambiguity in short UI strings, set `font-variant-ligatures: none`.

**Fallback stack:**
```css
--font-sans: 'Inter', system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
--font-mono: 'JetBrains Mono', 'Fira Code', 'Cascadia Code', 'SF Mono', 'Consolas', monospace;
```

---

### Type Scale

| Token | Size | Line Height | Weight | Letter Spacing | Usage |
|-------|------|-------------|--------|----------------|-------|
| `display-2xl` | 80px / `clamp(52px, 8vw, 80px)` | 0.95 | 800 | -0.04em | Hero H1 — "Full Double Diamond" |
| `display-xl` | 56px / `clamp(36px, 5vw, 56px)` | 1.05 | 700 | -0.03em | CTA section title, pull quote |
| `display-lg` | 40px / `clamp(28px, 4vw, 40px)` | 1.15 | 700 | -0.02em | Section H2 — "Each phase owns one cognitive mode" |
| `text-xl` | 26px | 1.35 | 500 | -0.01em | Blockquote, large callout text |
| `text-lg` | 19px | 1.55 | 400 | 0 | Hero sub, section lead copy |
| `text-md` | 16px | 1.70 | 400 | 0 | Body copy, card descriptions |
| `text-sm` | 14px | 1.65 | 400 | 0 | Secondary body, card p text, symptom list |
| `text-xs` | 13px | 1.50 | 400 | 0 | Footer, footnotes, timestamps |
| `label-lg` | 16px | 1.40 | 600 | -0.01em | Card H3, list item headings |
| `label-md` | 13px | 1.30 | 500 | 0 | Agent name, nav link, output tag |
| `label-sm` | 11px | 1.20 | 700 | +0.08em to +0.12em | Eyebrow / overline (uppercase) |
| `label-xs` | 10px | 1.20 | 700 | +0.06em to +0.10em | Pill label, column header (uppercase) |
| `code-md` | 14px | 1.60 | 400 | 0 | Command blocks, hero-cmd, cta-mono |
| `code-sm` | 12px | 1.50 | 500 | 0 | Output file paths, contract pills |
| `code-xs` | 10px | 1.40 | 700 | +0.05em | Agent output label, phase output tag |

**Negative letter-spacing rule:** Applied only at display-2xl through display-lg. Body text and UI labels use 0 or positive tracking. Uppercase eyebrow labels always use positive tracking (+0.08–0.12em) for legibility.

**Tabular numerals:** All counters, stats, and version numbers use `font-variant-numeric: tabular-nums` to prevent layout shift on update.

---

## Spacing Scale

**Base unit: 4px**

| Token | Value | Usage |
|-------|-------|-------|
| `space-1` | 4px | Icon gap, tight inline spacing |
| `space-2` | 8px | Badge padding, pill gap |
| `space-3` | 12px | Button horizontal padding (sm), list item gap |
| `space-4` | 16px | Card internal padding (sm), form field gap |
| `space-5` | 20px | Card padding, nav link gap |
| `space-6` | 24px | Card padding (md), section grid gap |
| `space-7` | 28px | Card padding (lg), card header bottom margin |
| `space-8` | 32px | Section sub-component gap |
| `space-10` | 40px | Wrap horizontal padding, stat row gap |
| `space-12` | 48px | Section header bottom margin |
| `space-14` | 56px | Pipeline header margin, contract section |
| `space-16` | 64px | — |
| `space-20` | 80px | Standard section vertical padding |
| `space-24` | 96px | — |
| `space-25` | 100px | Hero top padding |

**Content width:** `max-width: 960px` for the existing `.wrap`. Use `max-width: 80ch` (~720–800px depending on font size) for long-form prose columns. Nav and hero remain full-bleed within the wrap.

**Section rhythm:** All major sections use `padding: 80px 0` (space-20). Hero is 100px top / 80px bottom. This is a deliberate rhythm — do not deviate without reason.

---

## Border Radius

| Token | Value | CSS Variable | Usage |
|-------|-------|--------------|-------|
| `radius-xs` | 4px | — | Inline code chip (`cta-note code`), tight tag |
| `radius-sm` | 6px | — | Input fields, select, small badges |
| `radius-md` | 8px | — | Symptom row, check card, small status card |
| `radius-lg` | 12px | `--r` (existing) | Default card, modal, contract viz, output item |
| `radius-xl` | 16px | — | Large panel, feature section callout |
| `radius-full` | 9999px | — | Pills (`nav-badge`, `hero-eyebrow`, contract pills), avatar, status dot |

**Rationale:** The prototype uses `--r: 12px` as the only explicit radius token. The ramp above fills in the missing stops. `radius-lg` (12px) remains the default card radius, consistent with the Raycast / Vercel reference range of 6–12px. Pills are always `border-radius: 9999px` for language-independence.

---

## Elevation / Shadow

Dark themes use shadows sparingly — they are barely visible against deep backgrounds. Use `box-shadow` only to communicate interactivity, not to add depth for its own sake.

| Token | Value | Usage |
|-------|-------|-------|
| `shadow-none` | `none` | Resting card (border carries the edge) |
| `shadow-sm` | `0 1px 3px rgba(0,0,0,0.4), 0 1px 2px rgba(0,0,0,0.3)` | Hovered card, dropdown |
| `shadow-md` | `0 4px 12px rgba(0,0,0,0.5), 0 2px 4px rgba(0,0,0,0.3)` | Modal backdrop, elevated panel |
| `shadow-lg` | `0 8px 32px rgba(0,0,0,0.6), 0 4px 8px rgba(0,0,0,0.4)` | Full-page overlay, deep modals |
| `shadow-accent` | `0 0 0 4px rgba(91,110,245,0.15)` | Focus ring on interactive elements (used in `.agent:hover .agent-icon`) |
| `shadow-glow` | `0 0 24px rgba(91,110,245,0.08)` | Hero radial background glow — not interactive |

**Focus ring standard:** All keyboard-focusable elements use `box-shadow: shadow-accent` (4px accent ring) instead of `outline`, for consistency with the dark surface. Do not suppress outlines without providing this substitute.

---

## CSS Custom Properties — Complete Reference

The following block is the full token set as CSS custom properties, ready to paste into `:root`:

```css
:root {
  /* ── Surfaces ──────────────────────────────────── */
  --bg:              #080C14;
  --surface:         #0F1724;
  --surface-up:      #1A2436;
  --surface-overlay: #1E293B;

  /* ── Borders ───────────────────────────────────── */
  --border:          #1E2D44;
  --border-bright:   #2D4464;
  --border-accent:   rgba(91,110,245,0.25);

  /* ── Accent (Primary) ──────────────────────────── */
  --accent:          #5B6EF5;
  --accent-dark:     #3A4FD4;
  --accent-text:     #8B9EFF;
  --accent-glow:     rgba(91,110,245,0.12);
  --accent-border:   rgba(91,110,245,0.25);

  /* ── Text ──────────────────────────────────────── */
  --text:            #E8ECF4;   /* neutral-50 */
  --text-secondary:  #C8D0E0;   /* neutral-100 */
  --text-mid:        #8A8F98;   /* neutral-300 */
  --text-dim:        #4A5568;   /* neutral-400/500 */
  --text-muted:      #2D4464;   /* neutral-600 */

  /* ── Status ────────────────────────────────────── */
  --success:         #10B981;
  --success-text:    #34D399;
  --success-subtle:  rgba(16,185,129,0.12);
  --warning:         #F59E0B;
  --warning-text:    #FCD34D;
  --warning-subtle:  rgba(245,158,11,0.12);
  --error:           #EF4444;
  --error-text:      #FCA5A5;
  --error-subtle:    rgba(239,68,68,0.12);
  --info:            #3B82F6;
  --info-subtle:     rgba(59,130,246,0.12);

  /* ── Typography ────────────────────────────────── */
  --font-sans:  'Inter', system-ui, -apple-system, sans-serif;
  --font-mono:  'JetBrains Mono', 'Fira Code', 'Cascadia Code', monospace;

  /* ── Radius ────────────────────────────────────── */
  --r-xs:   4px;
  --r-sm:   6px;
  --r-md:   8px;
  --r:      12px;   /* existing --r preserved */
  --r-xl:   16px;
  --r-full: 9999px;

  /* ── Spacing ────────────────────────────────────── */
  --sp-1:  4px;   --sp-2:  8px;   --sp-3:  12px;  --sp-4:  16px;
  --sp-5:  20px;  --sp-6:  24px;  --sp-7:  28px;  --sp-8:  32px;
  --sp-10: 40px;  --sp-12: 48px;  --sp-14: 56px;  --sp-16: 64px;
  --sp-20: 80px;  --sp-24: 96px;  --sp-25: 100px;

  /* ── Shadows ────────────────────────────────────── */
  --shadow-sm:     0 1px 3px rgba(0,0,0,0.4), 0 1px 2px rgba(0,0,0,0.3);
  --shadow-md:     0 4px 12px rgba(0,0,0,0.5), 0 2px 4px rgba(0,0,0,0.3);
  --shadow-lg:     0 8px 32px rgba(0,0,0,0.6), 0 4px 8px rgba(0,0,0,0.4);
  --shadow-accent: 0 0 0 4px rgba(91,110,245,0.15);
  --shadow-glow:   0 0 24px rgba(91,110,245,0.08);
}
```
