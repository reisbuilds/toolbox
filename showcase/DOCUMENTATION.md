# toolbox

> A collection of reusable Claude Code skills that orchestrate multi-agent pipelines to produce design briefs and portfolio artifacts from any codebase.

**Status:** MVP · **License:** Not specified

---

## Overview

toolbox is a set of Claude Code skills for developers who need design foundations and portfolio artifacts but don't have a designer on the team. Each skill is a multi-agent pipeline: a thin orchestrator that sequences specialized sub-agents, extracts named structured outputs, and routes them to downstream agents as explicit data contracts — not conversation history.

The project addresses a specific failure mode in single-prompt AI generation: cross-phase incoherence. When you ask a model to produce a complete design brief or a full case study in one shot, each section is generated with the full original context active and no feedback loop between sections. The result is output that reads well locally but doesn't hold together across phases — color tokens that don't satisfy the contrast ratios mentioned two paragraphs later, case study decisions that don't match the problem statement, deck slides that feel disconnected from the narrative. toolbox solves this by breaking those tasks into discrete agent phases where each phase builds on verifiable output from the previous one.

The target user is a developer with a working codebase who needs to move from "I have no design direction" to "I have a token set, a component list, and a design brief I can actually implement from" — or from "I need a portfolio artifact for this project" to "I have documentation, a case study, and a slide deck." toolbox installs in under a minute and runs entirely inside Claude Code with no application runtime, no server, and no database.

---

## Features

### `/design-lifecycle`

- Reads the codebase — `package.json`, `src/` directory, existing component files, theme configurations — and classifies the application before generating anything
- Sources real-world UI references from production apps in the same genre via Mobbin MCP, falling back to targeted web searches when the MCP is unavailable
- Generates a complete design token set: hex color values with rationale sentences, type scale, spacing system, border radius decisions, and elevation levels
- Produces a component specification with ASCII wireframe layouts for every core user flow identified in Phase 1
- Runs a six-check verification pass — UI state coverage, approximate WCAG AA contrast ratios, touch target sizes, design system consistency, component-to-flow coverage, and Inspiration Summary alignment — before writing the final brief
- Writes a canonical `DESIGN_BRIEF.md` with explicit tone rules baked into the Amplifier's prompt: specific values not adjectives, no meta-commentary, no hedging

### `/project-showcase`

- Reads the codebase to produce a structured Project Profile: tech stack, architecture, core features, technically interesting decisions, inferred challenges, and code quality signals
- Writes comprehensive technical documentation: overview, architecture, setup, usage, design decisions, and honest known limitations
- Writes a portfolio case study: Context → Problem → Process → Solution → Outcomes → Reflection, first-person, specific, no buzzwords
- Validates both artifacts through a five-check quality gate before the deck is built: accuracy against Project Profile, voice and tone, completeness, specificity, and deck readiness
- Writes a Marp-compatible slide deck renderable to PDF or HTML with a single CLI command

### Shared Infrastructure

- Traveling data contracts: named structured blocks extracted verbatim from upstream agents and re-injected into downstream agents — each downstream agent receives exactly what it needs, not the full conversation history
- Orchestrators read all agent files in a pre-pass before spawning any agent, giving the orchestrator cross-phase context from the start
- All agents specify output format as an interface contract with exact heading names — the data contract is enforced at the agent level, not assumed at the orchestrator level

---

## Architecture

### System Overview

Each skill is a two-layer system. The orchestrator (`SKILL.md`) is a pure coordination layer: it reads agent files, sequences agents serially, extracts named structured blocks from each output, and injects them into downstream agents. It carries no domain knowledge. All domain knowledge — design expertise, portfolio writing standards, verification criteria — lives in the agent prompt files.

This separation makes individual agents replaceable without touching orchestration logic, and makes the orchestration logic stable because it is empty of the concerns it is coordinating.

### Tech Stack

| Layer | Technology | Why |
|-------|------------|-----|
| Orchestration | Claude Code skill system (`SKILL.md` + `agents/` convention) | Native multi-agent support; orchestrator reads agent files and spawns sub-agents without external infrastructure |
| Agent prompts | Markdown (prompt engineering) | Human-readable, version-controllable, directly executable by Claude Code |
| Deck rendering | Marp CLI | Renders Markdown slide decks to PDF or self-contained HTML; no design tool required |
| UI research | Mobbin MCP | Curated library of real product UI screenshots; graceful fallback to web search when unavailable |
| Inspiration fallback | Claude Code `WebSearch` | Covers Mobbin.com, NN Group, Smashing Magazine, Dribbble when Mobbin MCP is not configured |

### Project Structure

```
toolbox/
├── .claude/
│   └── skills/
│       ├── design-lifecycle/
│       │   ├── SKILL.md                 Orchestrator — sequences five agents, routes data contracts
│       │   ├── README.md                Setup and Mobbin MCP configuration
│       │   └── agents/
│       │       ├── 01-thinking-partner.md   Codebase classification; produces Project Brief
│       │       ├── 02-inspiration.md        UI reference sourcing; produces Inspiration Summary
│       │       ├── 03-maker.md              Token + component generation; produces .design/ files
│       │       ├── 04-verifier.md           Six-check audit; produces verification report
│       │       └── 05-amplifier.md          Synthesizes all phases into DESIGN_BRIEF.md
│       └── project-showcase/
│           ├── SKILL.md                 Orchestrator — sequences four agents, routes data contracts
│           ├── README.md                Setup and Marp rendering instructions
│           └── agents/
│               ├── 01-thinking-partner.md   Codebase framing; produces Project Profile
│               ├── 02-maker.md              Documentation + case study generation
│               ├── 03-validator.md          Five-check quality gate; produces Validation Report
│               └── 04-amplifier.md          Marp slide deck generation
├── showcase/                            Output directory for /project-showcase artifacts
├── .design/                             Output directory for /design-lifecycle artifacts
├── DESIGN_BRIEF.md                      Canonical design handoff for this project
└── README.md                            Skill reference and installation instructions
```

---

## Getting Started

### Prerequisites

- [Claude Code](https://claude.ai/code) installed and authenticated
- Git (for cloning)
- Node.js (only required for rendering the Marp slide deck to PDF or HTML)
- Mobbin paid plan (optional — only required for the `/design-lifecycle` Inspiration agent's Mobbin MCP path; web search fallback works without it)

### Installation

**All skills at once (available globally in every project):**

```bash
git clone https://github.com/reisbuilds/toolbox
cp -r toolbox/.claude/skills/. ~/.claude/skills/
```

**Individual skill — global:**

```bash
# Design Lifecycle only
cp -r toolbox/.claude/skills/design-lifecycle ~/.claude/skills/

# Project Showcase only
cp -r toolbox/.claude/skills/project-showcase ~/.claude/skills/
```

**Individual skill — per-project:**

```bash
# From inside your project directory
cp -r toolbox/.claude/skills/design-lifecycle .claude/skills/
cp -r toolbox/.claude/skills/project-showcase .claude/skills/
```

Once installed globally, invoke any skill by name in any Claude Code session.

### Optional: Mobbin MCP (for `/design-lifecycle`)

The Inspiration agent queries Mobbin for real product UI screenshots. Requires an active paid Mobbin plan.

```bash
claude mcp add mobbin --transport http https://api.mobbin.com/mcp
npx -y mobbin-mcp auth
```

Without this, the Inspiration agent falls back to web search automatically. The fallback produces useful results; Mobbin produces richer visual references.

### Rendering the Deck (for `/project-showcase`)

```bash
# Render to PDF
npx @marp-team/marp-cli showcase/DECK.md --pdf -o showcase/deck.pdf

# Render to self-contained HTML
npx @marp-team/marp-cli showcase/DECK.md -o showcase/deck.html
```

VS Code users: install [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) for live preview.

---

## Usage

### `/design-lifecycle`

Run from inside any project directory with a codebase:

```
/design-lifecycle
```

Runs the full five-phase pipeline against the current working directory. Produces `.design/design-tokens.md`, `.design/component-suggestions.md`, `.design/verification-report.md`, and `DESIGN_BRIEF.md`.

```
/design-lifecycle onboarding flow
```

Scopes the Thinking Partner's classification to the onboarding flow. Useful when the codebase is large and the relevant surface is a single feature.

```
/design-lifecycle checkout and payment
```

Focuses the design brief on the checkout and payment screens. The Inspiration agent will query for checkout-specific patterns.

```
/design-lifecycle dashboard and analytics
```

Produces a design brief scoped to the dashboard — data visualization conventions, density patterns, empty state handling for async data.

The pipeline outputs a live status checklist during execution:

```
Design Lifecycle — starting pipeline

  Phase 1 · Thinking Partner   ✅ Discover & Define
  Phase 2 · Inspiration        ✅ Mobbin / Web search
  Phase 3 · Maker              ⏳ Develop
  Phase 4 · Verifier           ⏳ Before Delivery
  Phase 5 · Amplifier          ⏳ Deliver & Operate
```

### `/project-showcase`

Run from inside any project directory:

```
/project-showcase
```

Runs the full four-phase pipeline. Produces `showcase/DOCUMENTATION.md`, `showcase/CASE_STUDY.md`, `showcase/VALIDATION_REPORT.md`, and `showcase/DECK.md`.

```
/project-showcase authentication flow
```

Scopes the Thinking Partner's analysis to the authentication flow — useful when you want the showcase artifacts to focus on a specific part of the project.

```
/project-showcase API design
```

Focuses the documentation and case study on the API design decisions. The Thinking Partner will read API route files and data contract patterns more deeply.

```
/project-showcase the onboarding experience
```

Scopes the artifacts to the onboarding experience. The case study's Process section will foreground the decisions made around that flow.

---

## Design Decisions

**The orchestrator carries zero domain knowledge**
`SKILL.md` is a pure coordination layer. It reads agent files, sequences agents, extracts named blocks, and injects them downstream. It does not generate design content, evaluate design quality, or interpret agent decisions. All domain expertise lives in the agent files. This makes orchestration logic stable and agents independently replaceable — changing an agent's approach requires only editing that agent's file, not touching the orchestrator.

**Pre-reading all agent files before spawning any agent**
The orchestrator reads every agent file in a single pass before the first sub-agent runs. This gives the orchestrator the full pipeline in context before execution begins — enabling it to reason about cross-phase dependencies rather than discovering each phase's requirements only when it arrives. The tradeoff is a longer startup pass before any visible work begins. It is worth it because the orchestrator can route outputs correctly without needing to re-read agent expectations at each handoff.

**Named structured output blocks as inter-agent data contracts**
Each agent produces a named structured block — the Project Brief, the Inspiration Summary, the Project Profile — with required heading names and exact fields. The orchestrator extracts those blocks verbatim and re-injects them into downstream agents as labeled, bounded context. A 200-token structured block keeps each downstream agent focused on what it actually needs rather than on parsing a 3,000-token conversation history. The tradeoff: enforcement is natural language, so format drift fails silently. Every agent's output format is specified as an interface contract to reduce drift, but there is no schema validation.

**Verifier and Validator placement before synthesis**
Both pipelines place a quality-check phase before the final synthesis agent. The Verifier in `/design-lifecycle` runs before the Amplifier writes `DESIGN_BRIEF.md`; the Validator in `/project-showcase` runs before the Amplifier writes `DECK.md`. The pattern is deliberate: failures caught before synthesis can be named in the final artifact as open items. Failures discovered after synthesis get inherited silently. The check has to happen while it can still block the output.

**Explicit anti-AI-voice rules in the Amplifier prompts**
Both Amplifier agents carry explicit tone rules: no meta-commentary, specific values not adjectives, no hedging language. These rules exist because LLMs degrade handoff artifacts in predictable ways — describing their own process, using adjectives where values should appear, qualifying every recommendation. The rules name and correct those specific failure modes directly in the prompt rather than trusting the model to avoid them without guidance.

---

## Known Limitations & What's Next

### Limitations

- **No schema enforcement on data contracts.** The contract between agents is enforced by heading names and block structure in natural language. An agent that drifts from its required format breaks extraction downstream silently — the orchestrator has no programmatic way to detect a malformed block.
- **No retry logic or failure recovery.** If an agent produces unusable output, the pipeline does not retry or surface a structured error. The run must be restarted from the beginning.
- **WCAG contrast checks are approximate.** The Verifier's contrast ratio assessment uses LLM-level hex arithmetic. It catches obvious failures; it does not replace a real accessibility audit tool like Lighthouse or axe.
- **Serial execution only.** All agents run in sequence. Phases that are theoretically independent — Thinking Partner and Inspiration in `/design-lifecycle`, for example — cannot be parallelized. Runtime is additive across all phases.
- **Less useful for mature design systems.** The token specifications generated by `/design-lifecycle` will conflict with established design conventions rather than extend them. The skill is most useful at greenfield or early maturity.
- **No automated test suite.** Quality is enforced by the Verifier and Validator agents at runtime, not by a test harness. Prompt regressions between Claude model versions are not automatically detectable.

### What's Next

- **Structured output contracts.** Replace heading-based extraction with typed JSON blocks in each agent's required output — making the data contract machine-readable, detectable when malformed, and recoverable without a full pipeline restart.
- **Verifier and Validator as blocking gates.** A fail-level finding should stop the pipeline and surface the error before the Amplifier runs, rather than passing a known failure into synthesis.
- **Incremental re-runs.** Allow the pipeline to re-run from a specific phase — regenerate the Maker output after manually editing token values, for example — without re-running upstream phases from scratch.
- **Parallel phase execution.** Where phases don't depend on each other's output, run them concurrently to reduce total pipeline runtime.
- **Output diffing.** When re-running a pipeline on a project that already has output files, surface a diff of what changed rather than silently overwriting prior artifacts.
