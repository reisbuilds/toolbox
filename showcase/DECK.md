---
marp: true
theme: gaia
paginate: true
---

<!-- _class: lead invert -->

# design-lifecycle

### Five agents. One command. A design brief you can actually build from.

---

## The Failure Mode Is Not a Model Problem

Ask one prompt to design a complete UI spec and you get:

- Color tokens that don't clear the contrast ratios cited two paragraphs later
- Component suggestions referencing a "Primary-500" that was never defined in the token table
- Empty states described in prose, absent from every screen layout

The output feels complete because it's long. It is not complete.

---

## The Insight: Four Cognitive Modes Cannot Share a Completion

Design is a sequence of decisions, not a generation event.

The Double Diamond maps naturally to distinct cognitive tasks:

- **Discover** — read signals, classify, frame the problem
- **Develop** — research analogous patterns, then specify
- **Verify** — audit the spec against hard thresholds
- **Deliver** — synthesize into durable, human-readable guidance

Each mode requires different context, different inputs, different outputs. Collapsing them produces telephone-game quality at each stage.

---

## The Architecture: One Orchestrator, Five Agents

| Phase | Agent | Output |
|-------|-------|--------|
| Discover & Define | Thinking Partner | Project Brief |
| Discover → Develop | Inspiration | Inspiration Summary |
| Develop | Maker | `design-tokens.md` + `component-suggestions.md` |
| Before Delivery | Verifier | `verification-report.md` |
| Deliver | Amplifier | `DESIGN_BRIEF.md` |

One orchestrator. No design knowledge at the top level. That separation is the whole design.

---

## The Orchestration Layer Does One Thing

SKILL.md is a pure coordination layer. It:

- Reads all five agent files up front — one I/O pass before anything is spawned
- Extracts named structured blocks from each agent's output
- Injects only what each downstream agent needs — not the full conversation history
- Displays a live phase checklist as the pipeline runs

Token counts stay manageable. Agents stay focused. The orchestrator stays out of the way.

---

## Data Contracts Prevent Drift

Two structured blocks are the connective tissue of the pipeline:

**Project Brief** — produced by the Thinking Partner, passed verbatim to every subsequent agent (Phases 2 through 5). Genre, platform, design maturity, core flows, tone keywords, design problem statement. The Amplifier sees the same brief the Maker saw — not a paraphrase of it.

**Inspiration Summary** — produced by the Inspiration agent, passed to the Maker and Amplifier. Navigation pattern, color temperature, layout density, CTA placement.

A 200-token structured block is not a 3,000-token conversation history. These two blocks are the architecture. Everything else is implementation.

---

<!-- _class: lead invert -->

## Most Pipelines: Generate → Deliver
## This One: Generate → Verify → Deliver

The Verifier runs six checks with exact thresholds before the Amplifier writes anything:

1. UI state coverage per core flow
2. WCAG AA contrast ratios — 4.5:1 normal text, 3:1 large text and UI components
3. Touch targets — 44×44pt iOS / 48×48dp Android
4. Internal design system consistency
5. Component coverage versus core flows
6. Alignment between tokens and Inspiration Summary recommendations

Failures caught here get fixed. Failures that reach `DESIGN_BRIEF.md` become the implementation standard.

---

## What You Get: Four Files That Survive the Conversation

| File | What It Contains |
|------|-----------------|
| `.design/design-tokens.md` | Color palette with hex values and rationale sentences, full type scale, spacing, border radius, shadow levels |
| `.design/component-suggestions.md` | Three-layer component hierarchy, ASCII wireframe layouts per core flow, interaction specs for every UI state |
| `.design/verification-report.md` | Six-check audit with ✅ / ⚠️ / ❌ status per item, contrast ratios, touch target results |
| `DESIGN_BRIEF.md` | Canonical handoff document — app context, design philosophy, full specs, at least five documented decisions with tradeoffs |

Committed to disk. Versioned. Survive the conversation.

---

## What Was Hard

**Defining the data contract before writing the agents.**
The hardest problem surfaced too late: what fields does the Project Brief need? How is the Inspiration Summary structured? Agent prompt quality depends on the contract being pre-defined, not negotiated. Iterating on prompt quality before locking the contract meant rewriting prompts when the contract changed.

**Preventing cognitive mode bleeding.**
Each agent has to stay in its lane. The Verifier does not redesign — it audits. The Inspiration agent does not specify tokens — it synthesizes reference signals. Writing clear output contracts for each agent was the mechanism; the temptation to let agents do "a little of the next phase" was real and would have re-introduced the original failure mode.

---

## What's Next

- **Phase 0 pre-flight** — read the project's existing design system (Tailwind config, CSS custom properties, theme files) and pass it to the Maker as a constraint set, so generated tokens extend rather than replace what's already there
- **Diff mode** — run the pipeline against a feature branch and produce a design-delta report showing what changed relative to the baseline brief
- **Precise contrast checks** — replace approximate hex-value ratio calculation with a programmatic WCAG contrast check against actual token values, eliminating the "approximate" qualifier from the Verifier's output

---

<!-- _class: lead invert -->

# design-lifecycle

`/design-lifecycle` · `.claude/skills/design-lifecycle/`
