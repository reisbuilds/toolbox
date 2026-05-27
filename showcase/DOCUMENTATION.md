# design-lifecycle

> A five-agent orchestration system that generates a structured, verifiable design system from any codebase — for developers who need real design foundations before they start building.

**Status:** MVP · **Invocation:** `/design-lifecycle` or `/design-lifecycle <focus area>`

---

## Overview

When a developer asks Claude to "help with design," they get a response. It is internally coherent, plausible, and largely useless — because it was generated in one pass, without grounding in the actual codebase, without research into real products in the same genre, and without verification that any of it holds together. Tokens don't reference the inspiration. Components don't map to identified flows. The advice arrives fully formed and fully detached.

`design-lifecycle` solves a different problem. It reads the codebase first — `package.json`, `src/` structure, existing component files, theme configurations — and classifies the application before generating anything. That classification travels as a structured data contract to every downstream agent. What you get at the end is not a response. It is four files: a token set, a component list, a verification report, and a design brief.

This skill is for developers who have a codebase but no designer, no visual language, and no clear starting point. It is not for generating quick mockup ideas. It is for building a real design foundation — one where the recommendations trace back to what the application actually is.

---

## How It Works

Before spawning any agent, the orchestrator reads all five agent files in a single pre-pass. This matters because it means the orchestrator has the full pipeline in context before execution begins — enabling cross-phase reasoning from the start, not just sequential handoffs. It knows what every agent will need before the first agent runs.

The pipeline then executes serially. Each agent produces a structured output with required headings. The orchestrator extracts named blocks from those outputs and re-injects them into downstream agents as explicit context — not as raw text dumps, but as identified, labeled structured blocks.

### Pipeline

| Phase | Agent | Input | Output |
|-------|-------|-------|--------|
| 1 | Thinking Partner | Codebase files (`package.json`, `src/`, component files, theme files) | Project Brief (genre, platform, maturity, classification) |
| 2 | Inspiration | Project Brief | Inspiration Summary (real products, visual references, tone patterns) |
| 3 | Maker | Project Brief + Inspiration Summary | `design-tokens.md`, `component-suggestions.md` |
| 4 | Verifier | Project Brief + Maker output | Verification report (six-check audit, pass/warn/fail per check) |
| 5 | Amplifier | Project Brief + Inspiration Summary + Maker output + Verifier report | `DESIGN_BRIEF.md` |

### Data Contracts

The Project Brief produced in Phase 1 flows to Phases 3, 4, and 5. Every downstream agent operates from the same upstream classification — not from its own interpretation of the Maker's output. This is what makes the Verifier's audit meaningful: it audits against the original classification, not against the design recommendations in isolation.

The Inspiration Summary produced in Phase 2 flows to Phases 3 and 5. The Maker uses it to ground token decisions in real-world products. The Amplifier uses it to write `DESIGN_BRIEF.md` with accurate visual references.

### Output Files

| File | Location | Contents |
|------|----------|----------|
| `design-tokens.md` | Project root | Color palette (hex values with rationale), type scale table, spacing system, border radius, elevation |
| `component-suggestions.md` | Project root | Prioritized component list mapped to identified user flows |
| Verification report | Orchestrator output | Six discrete checks with pass/warn/fail severity |
| `DESIGN_BRIEF.md` | Project root | Canonical handoff artifact written for human designers and developers |

---

## The Agents

**Thinking Partner** reads the codebase — `package.json` for dependencies and framework, `src/` structure for application shape, existing component files for current conventions, theme files for any design decisions already made — and classifies the application by genre, platform, and maturity level. It produces the Project Brief: a structured block with named fields that every downstream agent depends on. Classification before generation is not a delay. It is the foundation everything else stands on.

**Inspiration** takes the Project Brief and finds real-world products in the same genre. It queries the Mobbin MCP first — a curated library of real product UI patterns — and falls back to targeted web searches against Mobbin.com, NN Group, Smashing Magazine, and Dribbble when the MCP is unavailable. It produces the Inspiration Summary: named products, specific UI patterns, visual tone observations. Not general trends. Specific references the Maker can trace decisions back to.

**Maker** takes the Project Brief and Inspiration Summary and writes two files. `design-tokens.md` contains a full color palette with hex values and rationale sentences per color, a type scale table, a spacing system, border radius decisions, and elevation levels. `component-suggestions.md` contains a prioritized component list mapped to the user flows identified in the Project Brief. The Maker does not invent from scratch. It builds from what Phase 1 classified and Phase 2 sourced.

**Verifier** does not redesign. It audits. It runs six discrete checks against the Maker's output — using the Project Brief as the reference, not the design recommendations themselves — and produces a pass/warn/fail signal for each check before the Amplifier runs. One of those checks is an approximate WCAG contrast ratio assessment; the report is honest that this is LLM-level hex arithmetic, not a precise accessibility tool. The Verifier exists because a pipeline that generates and immediately synthesizes produces a final artifact that inherits all unchecked problems silently.

**Amplifier** synthesizes all four phases into `DESIGN_BRIEF.md`. It receives the Project Brief, Inspiration Summary, Maker output, and Verifier report as named structured blocks, and writes a single canonical document for human designers and developers. The Amplifier's output rules are explicit: no meta-commentary, specific values not adjectives, no hedging. These rules exist because LLMs default to hedging, vagueness, and self-description. The Amplifier's job is to produce a document that reads as if a thoughtful designer wrote it — because that is what a handoff artifact is for.

---

## Getting Started

### Prerequisites

- Claude Code with multi-agent skill support
- A codebase with at least a `package.json` and a `src/` directory
- Mobbin MCP configured (optional — the Inspiration agent degrades gracefully to web search fallback)

### Invocation

```
/design-lifecycle
```
Runs the full five-phase pipeline against the current working directory.

```
/design-lifecycle focus on the orchestration layer
```
Passes a focus area to the Thinking Partner, which uses it to weight its classification — useful when the codebase is large and the relevant surface is narrower.

```
/design-lifecycle src/features/checkout
```
Scopes the codebase read to a specific subdirectory when the relevant code is isolated.

---

## Output Files

| File | Location | Contents |
|------|----------|----------|
| `design-tokens.md` | Project root | Color palette with hex values and rationale, type scale, spacing system, border radius, elevation |
| `component-suggestions.md` | Project root | Component list prioritized by user flow, with mapping notes |
| `DESIGN_BRIEF.md` | Project root | Canonical design handoff document — written for humans, not for Claude |

The verification report appears in the orchestrator's output stream during Phase 4. It is not written to a file — it is a quality signal that gates the Amplifier, not a deliverable.

---

## Design Decisions

**Pre-reading all agent files before spawning any agent**
The orchestrator reads all five agent files in one pass before execution begins. This gives the orchestrator the full pipeline in context before the first agent runs — enabling it to reason about cross-phase dependencies, not just pass outputs forward blindly. The tradeoff is a longer startup pass. It is worth it.

**The Project Brief as a traveling data contract**
The Project Brief is a named, structured block extracted from the Thinking Partner's output and re-injected — verbatim, labeled — into Phases 3, 4, and 5. This means the Verifier audits against the same classification the Maker built from, not against its own reading of the design output. The tradeoff is fragility: if the Thinking Partner doesn't produce the exact required headings, extraction fails. Every agent's output format is specified as an interface, not a suggestion, to address this.

**The Verifier as a discrete phase before synthesis**
Most generation pipelines go: generate → deliver. This one goes: generate → verify → deliver. Placing the Verifier before the Amplifier forces a structured quality signal before the final artifact is written. The Amplifier receives the Verifier's report and can name open items explicitly in `DESIGN_BRIEF.md`, rather than silently inheriting unchecked problems. It is a small architectural choice with a large effect on output honesty.

---

## Known Limitations

**The orchestration logic lives in natural language.** There is no retry logic, no failure recovery, no typed schema validation. If an agent produces malformed output, the orchestrator has no programmatic way to detect or recover from it. The output format requirements in each agent file are the only enforcement mechanism.

**WCAG contrast checks are approximate.** The Verifier's contrast ratio check uses LLM-level hex arithmetic. It catches obvious failures and flags marginal cases — it does not replace a real accessibility audit tool.

**The pipeline is serial and cannot be parallelized.** Phases 1 and 2 could theoretically run independently of each other. They don't — the Inspiration agent uses the Project Brief. This is by design, but it means runtime is additive across all five phases.

**Extraction depends on exact output format.** The data contract between agents is enforced by heading names and block structure in natural language. An agent that drifts from its required format breaks extraction downstream. There is no schema enforcement, no structured data type, no fallback.

---

## What's Next

- **Structured output contracts.** Replace heading-based extraction with typed JSON blocks in each agent's output — making the data contract explicit and machine-readable rather than pattern-matched from prose.
- **Verifier as a blocking gate.** Currently the Amplifier runs regardless of Verifier severity. A fail-level finding should stop the pipeline and surface the error before `DESIGN_BRIEF.md` is written.
- **Incremental re-runs.** Allow the pipeline to re-run from a specific phase — regenerate only the Maker output, for example, and re-verify — without re-running Thinking Partner and Inspiration from scratch.
