# toolbox

> A collection of reusable Claude Code skills that orchestrate multi-agent pipelines to produce design briefs and portfolio artifacts from any codebase.

**Status:** MVP · **License:** Not specified

---

## Overview

toolbox is a set of Claude Code skills for developers who need design foundations or portfolio artifacts but don't have a designer or technical writer on the team. Each skill is a multi-agent pipeline: a thin orchestrator that sequences specialized sub-agents, extracts named structured outputs from each, and routes them to downstream agents as explicit data contracts — not conversation history. Installing a skill takes under a minute. Running it requires no application runtime, no server, and no database.

The project targets a specific failure mode in single-prompt AI generation: cross-phase incoherence. When you ask a model to produce a complete design brief or case study in one completion, each section generates with the full original context active and no feedback loop between sections. The result holds together locally but not across phases — color tokens that don't satisfy the contrast ratios mentioned two paragraphs later, case study decisions that don't match the problem statement, slides that feel disconnected from the story. toolbox solves this by decomposing those tasks into discrete agent phases where each phase builds on verifiable, structured output from the previous one.

The target user is a developer with a working codebase who needs to move from "I have no design direction" to "I have a token set, a component list, and a design brief I can implement from" — or from "I need to document and present this project" to "I have documentation, a case study, and a slide deck ready to share." Both transitions require the same underlying infrastructure: phase separation, explicit data contracts, and a quality gate before synthesis.

---

## Features

### `/design-lifecycle`

- Reads the codebase — `package.json`, `src/` directory tree, existing component files, and theme configurations — and classifies the application before generating anything
- Sources real-world UI references from production apps in the same genre via Mobbin MCP, falling back to targeted web search automatically when Mobbin is unavailable
- Generates a complete design token set: hex color values with rationale sentences, a full type scale, a spacing system, border radius tokens, and elevation levels
- Produces a component specification with a prioritized component hierarchy and ASCII wireframe layouts for every core user flow identified in Phase 1
- Runs a six-check verification pass before writing the final brief: UI state coverage, approximate WCAG AA contrast ratios, touch target sizes, design system consistency, component-to-flow coverage, and Inspiration Summary alignment
- Writes a canonical `DESIGN_BRIEF.md` with explicit anti-AI-voice rules baked into the Amplifier prompt: specific values not adjectives, no meta-commentary, no hedging

### `/project-showcase`

- Reads the codebase to produce a structured Project Profile covering tech stack, architecture, core features, technically interesting decisions, inferred challenges, and code quality signals
- Writes comprehensive technical documentation with overview, architecture, setup, usage, design decisions, and an honest known-limitations section
- Writes a portfolio case study following the Context → Problem → Process → Solution → Outcomes → Reflection structure, first-person, specific, no buzzwords
- Validates both artifacts through a five-check quality gate before the deck is built: accuracy against the Project Profile, voice and tone, completeness, specificity, and narrative arc readiness for the slide deck
- Writes a Marp-compatible slide deck renderable to PDF or HTML with a single CLI command

### Shared Infrastructure

- **Traveling data contracts.** Named structured blocks — the Project Brief, the Inspiration Summary, the Project Profile — are extracted verbatim from upstream agents and re-injected into downstream agents as labeled, bounded context. Each downstream agent receives exactly what it needs, not the full conversation history.
- **Pre-pass agent loading.** Orchestrators read all agent files in a single pass before spawning any sub-agent, giving the orchestrator cross-phase context before execution begins.
- **Output format as interface contract.** Every agent specifies its required output format with exact heading names and block structure. The data contract is enforced at the agent level, not assumed at the orchestrator level.

---

## Architecture

### System Overview

Each skill is a two-layer system. The orchestrator (`SKILL.md`) is a pure coordination layer: it reads agent files in a pre-pass, sequences agents serially, extracts named structured blocks from each output, and injects them into downstream agents. It carries no domain knowledge whatsoever — no design expertise, no writing standards, no verification criteria. All domain knowledge lives in the agent prompt files.

This separation makes individual agents replaceable without touching orchestration logic. The orchestration layer is stable because it is empty of the concerns it is coordinating. Updating the Verifier's contrast check criteria requires editing only `agents/04-verifier.md` — the orchestrator's behavior does not change.

### Tech Stack

| Layer | Technology | Why |
|-------|------------|-----|
| Orchestration | Claude Code skill system (`SKILL.md` + `agents/` convention) | Native multi-agent support; orchestrator reads agent files and spawns sub-agents without external infrastructure |
| Agent prompts | Markdown (prompt engineering) | Human-readable, version-controllable, directly executable by Claude Code |
| Deck rendering | Marp CLI | Renders Markdown slide decks to PDF or self-contained HTML; no design tool required |
| UI research | Mobbin MCP | Curated library of real product UI screenshots accessible via MCP protocol |
| Inspiration fallback | Claude Code `WebSearch` | Covers Mobbin.com, NN Group, Smashing Magazine, and Dribbble when Mobbin MCP is not configured |

### Project Structure

```
toolbox/
├── .claude/
│   └── skills/
│       ├── design-lifecycle/
│       │   ├── SKILL.md                     Orchestrator — sequences five agents, routes data contracts
│       │   ├── README.md                    Setup and Mobbin MCP configuration
│       │   └── agents/
│       │       ├── 01-thinking-partner.md   Codebase classification; produces Project Brief
│       │       ├── 02-inspiration.md        UI reference sourcing; produces Inspiration Summary
│       │       ├── 03-maker.md              Token + component generation; writes .design/ files
│       │       ├── 04-verifier.md           Six-check audit; writes verification-report.md
│       │       └── 05-amplifier.md          Synthesizes all phases into DESIGN_BRIEF.md
│       └── project-showcase/
│           ├── SKILL.md                     Orchestrator — sequences four agents, routes data contracts
│           ├── README.md                    Setup and Marp rendering instructions
│           └── agents/
│               ├── 01-thinking-partner.md   Codebase framing; produces Project Profile
│               ├── 02-maker.md              Documentation + case study generation
│               ├── 03-validator.md          Five-check quality gate; writes Validation Report
│               └── 04-amplifier.md          Marp slide deck generation
├── showcase/                                Output directory for /project-showcase artifacts
├── .design/                                 Output directory for /design-lifecycle artifacts
├── DESIGN_BRIEF.md                          Canonical design handoff for this project (self-generated)
└── README.md                                Skill reference and installation instructions
```

---

## Getting Started

### Prerequisites

- [Claude Code](https://claude.ai/code) installed and authenticated
- Git (for cloning)
- Node.js (only required for rendering the Marp deck to PDF or HTML)
- Mobbin paid plan (optional — only required for the Inspiration agent's Mobbin MCP path; web search fallback works without it)

### Installation

**All skills at once — available globally in every project:**

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

**Individual skill — scoped to a specific project:**

```bash
# From inside your project directory
cp -r toolbox/.claude/skills/design-lifecycle .claude/skills/
cp -r toolbox/.claude/skills/project-showcase .claude/skills/
```

Once installed globally, invoke any skill by name in any Claude Code session.

### Optional: Mobbin MCP Setup

The Inspiration agent in `/design-lifecycle` queries Mobbin for real product UI screenshots. Requires an active paid Mobbin plan. Without this, the agent falls back to web search automatically.

```bash
claude mcp add mobbin --transport http https://api.mobbin.com/mcp
npx -y mobbin-mcp auth
```

Verify the integration by typing `/mcp` in any Claude Code session — `mobbin` should appear in the list.

### Rendering the Deck

The Marp deck produced by `/project-showcase` renders to PDF or standalone HTML:

```bash
# Render to PDF
npx @marp-team/marp-cli showcase/DECK.md --pdf -o showcase/deck.pdf

# Render to self-contained HTML (shareable without dependencies)
npx @marp-team/marp-cli showcase/DECK.md -o showcase/deck.html
```

VS Code users: install [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) for live preview and direct export.

---

## Usage

### `/design-lifecycle`

Run from inside any project directory with a codebase:

```
/design-lifecycle
```

Runs the full five-phase pipeline against the current working directory. Produces `.design/design-tokens.md`, `.design/component-suggestions.md`, `.design/verification-report.md`, and `DESIGN_BRIEF.md` at the project root.

```
/design-lifecycle onboarding flow
```

Scopes the Thinking Partner's analysis to the onboarding flow. The Inspiration agent queries for onboarding-specific UI patterns. Maker generates component layouts for onboarding screens specifically. Useful when the codebase is large and the design surface you care about is a single feature.

```
/design-lifecycle checkout and payment
```

Focuses the design brief on checkout and payment screens — the Inspiration agent will look for checkout-specific patterns, and the component specification will prioritize payment form layouts, order summary cards, and confirmation states.

```
/design-lifecycle dashboard and analytics
```

Produces a brief scoped to data visualization conventions, density patterns, empty state handling for async data, and chart component structures.

The pipeline outputs a live status checklist during execution:

```
Design Lifecycle — starting pipeline

  Phase 1 · Thinking Partner   ✅ Discover & Define
  Phase 2 · Inspiration        ✅ Mobbin / Web search
  Phase 3 · Maker              ⏳ Develop
  Phase 4 · Verifier           ⏳ Before Delivery
  Phase 5 · Amplifier          ⏳ Deliver & Operate
```

**Output files:**

```
.design/
  design-tokens.md          Color palette, type scale, spacing, shadows
  component-suggestions.md  Component hierarchy, screen layouts, interaction patterns
  verification-report.md    Six-check audit: states, contrast, coverage, consistency
DESIGN_BRIEF.md             Canonical design handoff — philosophy, tokens, components, rationale
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

Scopes the Thinking Partner's deep-read and all downstream artifacts toward the authentication flow. The case study's Process section foregrounds decisions made around that flow specifically.

```
/project-showcase API design
```

Focuses the documentation and case study on API design decisions. The Thinking Partner reads API route files and data contract patterns more deeply.

```
/project-showcase the onboarding experience
```

Produces documentation and a case study centered on onboarding UX decisions, with the Thinking Partner sampling onboarding components, flows, and state management specifically.

**Output files:**

```
showcase/
  DOCUMENTATION.md      Comprehensive technical + usage docs
  CASE_STUDY.md         Portfolio case study narrative
  VALIDATION_REPORT.md  Quality gate findings
  DECK.md               Marp slide deck — render to PDF or HTML
```

---

## Design Decisions

**The orchestrator carries zero domain knowledge.**
`SKILL.md` in each skill is a pure coordination layer. It reads agent files, sequences agents, extracts named blocks, and injects them downstream. It does not generate design content, evaluate design quality, or interpret agent decisions. All expertise lives in the agent files. This makes orchestration logic stable — changing an agent's approach requires editing only that agent's file — and makes agents independently revisable because they are the only holders of their domain knowledge. The tradeoff: the orchestrator cannot detect or recover from malformed agent output, because doing so would require domain knowledge it explicitly does not have.

**Pre-reading all agent files before spawning any agent.**
The orchestrator reads every agent file in a single pass before the first sub-agent runs. This gives the orchestrator the full pipeline in scope before execution begins, enabling it to route outputs correctly without re-reading agent expectations at each handoff. The tradeoff is a longer startup pass before any visible work begins. It is worth it: the alternative is discovering each downstream agent's requirements only when the orchestrator arrives at that phase, which creates fragile sequencing logic.

**Named structured output blocks as inter-agent data contracts.**
Each agent produces a named structured block — the Project Brief, the Inspiration Summary, the Project Profile, the Case Study Narrative — with required heading names and exact fields. The orchestrator extracts those blocks verbatim and re-injects them as labeled, bounded context. A 200-token structured block keeps each downstream agent focused on what it needs rather than on parsing a 3,000-token conversation history. The tradeoff: enforcement is natural language, so format drift fails silently. Every agent's output format is specified as an interface contract to reduce that risk, but there is no schema validation at the orchestrator level.

**Verifier and Validator placement before synthesis.**
Both pipelines place a quality-check phase before the final synthesis agent. The Verifier in `/design-lifecycle` runs before the Amplifier writes `DESIGN_BRIEF.md`; the Validator in `/project-showcase` runs before the Amplifier writes `DECK.md`. The reason is specific: failures caught before synthesis can be named in the final artifact as open items. Failures inherited after synthesis become institutional memory — future implementers work from the brief, not from the audit report. The check has to happen while it can still block the output.

**Explicit anti-AI-voice rules in the Amplifier prompts.**
Both Amplifier agents carry explicit tone rules: no meta-commentary, specific values not adjectives, no hedging language. These rules exist because language models degrade handoff artifacts in predictable ways — describing their own process, using adjectives where values should appear, qualifying every recommendation with uncertainty hedges. The rules name and correct those specific failure modes directly in the prompt rather than trusting the model to avoid them without guidance.

---

## Known Limitations & What's Next

### Limitations

- **No schema enforcement on data contracts.** The contract between agents is enforced by heading names and block structure in natural language. An agent that drifts from its required format breaks extraction downstream silently — the orchestrator has no programmatic way to detect a malformed block.
- **No retry logic or failure recovery.** If an agent produces unusable output, the pipeline does not retry or surface a structured error. The run must be restarted from the beginning.
- **WCAG contrast checks are approximate.** The Verifier's contrast ratio assessment uses LLM-level hex arithmetic. It catches obvious failures but does not replace a real accessibility audit tool like Lighthouse or axe.
- **Serial execution only.** All agents run in sequence. Phases that are theoretically independent cannot be parallelized in the current implementation. Runtime is additive across all phases.
- **Less useful for mature design systems.** The token specifications generated by `/design-lifecycle` will conflict with established design conventions rather than extend them. The skill is most useful at greenfield or early-maturity projects.
- **No automated test suite.** Quality is enforced by the Verifier and Validator agents at runtime, not by a test harness. Prompt regressions across Claude model versions are not automatically detectable.
- **Focus area is a loose string.** The `$ARGUMENTS` value passed as a focus area is a plain string. The Thinking Partner interprets it with no structured guidance on which specific flows are in scope versus out of scope.

### What's Next

- **Structured output contracts.** Replace heading-based extraction with typed JSON blocks in each agent's required output — making the data contract machine-readable, detectable when malformed, and recoverable without a full pipeline restart.
- **Verifier and Validator as hard blocking gates.** A fail-level finding should stop the pipeline before the Amplifier runs and surface the error explicitly, rather than passing a known failure into synthesis.
- **Incremental re-runs.** Allow the pipeline to restart from a specific phase — regenerate only the Maker output after manually editing token values, for example — without re-running all upstream phases from scratch.
- **Parallel phase execution.** Where phases don't depend on each other's output, run them concurrently to reduce total pipeline runtime.
- **Structured focus area scoping.** Replace the `$ARGUMENTS` string with a structured focus object specifying feature name, affected flows, and explicitly out-of-scope areas — producing tighter, more coherent downstream artifacts.
