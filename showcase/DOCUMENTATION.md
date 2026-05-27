# design-lifecycle

> A five-agent orchestration system that generates a structured, verifiable design system from any codebase — for developers who need real design foundations before they start building.

**Status:** MVP · **Invocation:** `/design-lifecycle` or `/design-lifecycle <focus area>`

---

## Overview

Coherent design output requires a sequence of distinct cognitive tasks: discovery, inspiration research, specification, verification, and synthesis. Collapse all five into one prompt and you get output that sounds authoritative but falls apart under scrutiny — contrast ratios that don't clear WCAG AA, component names that reference tokens never defined, empty states described in prose but absent from every layout. The output looks complete because it's long. It is not complete.

This skill is for developers and technical PMs who have a codebase and need a real design foundation — tokens, components, interaction patterns — before building UI, without a designer on the team. It runs entirely from Claude Code. It reads the project directory, infers stack and genre, gathers real-world UI references, produces concrete token and component specs with hex values and rationale sentences, validates them against six discrete criteria, and writes everything to four files.

What separates this from asking Claude to "help with design" is the pipeline contract. Each agent receives typed structured inputs — a Project Brief, an Inspiration Summary — not a conversation thread. The Verifier runs as a mandatory quality gate before the Amplifier synthesizes anything. Downstream agents cannot drift from the original design intent because they receive the Project Brief directly, not a paraphrase of it.

---

## How It Works

The orchestrator reads all five agent files in a single pass before spawning any sub-agent. This front-loads the file I/O cost and ensures every agent prompt is in scope when the pipeline begins. The orchestrator has the full pipeline structure in memory before it makes any decisions about sequencing or routing.

Each phase produces a typed output. The orchestrator extracts named structured blocks — the **Project Brief** and the **Inspiration Summary** — and injects them explicitly into downstream agents. They are passed verbatim, not summarized.

| Phase | Agent | Input | Output |
|-------|-------|-------|--------|
| Discover & Define | Thinking Partner | Project working directory, optional focus area | Project Brief (structured block) |
| Discover → Develop | Inspiration | Project Brief | Inspiration Summary (structured block) |
| Develop | Maker | Project Brief + Inspiration Summary | `.design/design-tokens.md`, `.design/component-suggestions.md` |
| Before Delivery | Verifier | Project Brief + Inspiration Summary + both Maker files | `.design/verification-report.md` |
| Deliver & Operate | Amplifier | Project Brief + Inspiration Summary + all three `.design/` files | `DESIGN_BRIEF.md` (project root) |

The Project Brief flows to every agent in Phases 2 through 5. The Inspiration Summary flows to Phases 3 and 5. Neither gets summarized or rephrased at each hop — every downstream agent is anchored to the same upstream classification, not a telephone-game version of it.

If the Verifier flags ❌ failures or ⚠️ warnings, those surface in the orchestrator's final summary. The Amplifier receives the full verification report and carries open items forward into `DESIGN_BRIEF.md`. Gaps are visible, not buried.

---

## The Agents

### Thinking Partner

The Thinking Partner classifies. It reads project signals — `package.json`, `README.md`, route and component files, existing theme configs — and produces a genre, platform, and design maturity classification. It maps 3–5 core user flows, derives 4–6 tone keywords, and frames a one-to-two sentence design problem statement. Everything downstream depends on this classification being correct. Its output is a strictly formatted **Project Brief** block that every subsequent agent receives.

### Inspiration

The Inspiration agent bridges Discover and Develop. It receives the Project Brief and searches for 5–8 real-world UI references matching the app's genre and core flows. Mobbin MCP is the primary source — the agent calls `search_screens` and `get_screen_detail` for up to three queries. If Mobbin is unavailable, it falls back to WebSearch against Mobbin, Dribbble, Nielsen Norman Group, and Smashing Magazine. From those references it synthesizes concrete design signals: navigation pattern, color temperature, typography approach, layout density, CTA placement, and micro-interaction patterns. It emits a structured **Inspiration Summary** block.

### Maker

The Maker specifies. It receives the Project Brief and Inspiration Summary and produces two files. `design-tokens.md` defines the visual foundation: a color palette with hex values and rationale sentences, a full type scale with eight size tokens, a spacing scale, border radius tokens, and 3–4 shadow levels. `component-suggestions.md` defines a three-layer component hierarchy — Primitives, Composed, Feature Components — ASCII wireframe layouts per core flow, and interaction pattern specs for empty, loading, error, success, and partial-data states. Both files are written to `.design/` and are immediately usable.

### Verifier

The Verifier does not redesign. It audits. It runs six discrete checks against the Maker's output: UI state coverage per flow, WCAG AA contrast ratios (4.5:1 for normal text, 3:1 for large text and UI components), touch target minimums (44×44pt iOS / 48×48dp Android for mobile platforms), design system internal consistency, component coverage versus core flows, and alignment between design tokens and the Inspiration Summary's recommendations. Each finding is marked ✅, ⚠️, or ❌. The output is written to `.design/verification-report.md`.

### Amplifier

The Amplifier synthesizes. It receives every output from the pipeline and writes a single `DESIGN_BRIEF.md` at the project root — a canonical handoff document written for humans, not for the pipeline. The brief includes app context, design philosophy, condensed inspiration references, full token and component specs, a verification status table with open items, and a Design Decisions & Rationale section with at least five documented choices. That rationale section is the durable part: it records why decisions were made so future contributors do not accidentally reverse them. The Amplifier also writes separate next-step checklists for designers, developers, and the team.

---

## Getting Started

**Prerequisites:**
- Claude Code installed
- The skill registered at `.claude/skills/design-lifecycle/`
- A project codebase in the working directory (any language or framework)

**Invocation:**
```bash
/design-lifecycle                    # full project analysis
/design-lifecycle authentication     # scoped to a specific flow
/design-lifecycle dashboard          # scoped to a feature
```

The focus argument scopes the Thinking Partner's analysis and shapes the Maker's component layouts to that area. The broader project directory is still read for context.

**Optional — Mobbin MCP (recommended):**

The Inspiration agent uses Mobbin to pull real UI screenshots from production apps. Without it, the agent falls back to WebSearch — still functional, but Mobbin gives richer, more curated references. Mobbin requires an active paid subscription.

```bash
# Add the MCP server
claude mcp add mobbin --transport http https://api.mobbin.com/mcp

# Authenticate
npx -y mobbin-mcp auth

# Verify — type /mcp in any Claude Code session; mobbin should appear
```

Full Mobbin MCP docs: https://docs.mobbin.com/mcp/clients/claude-code

---

## Output Files

Four files are written per run. All are versioned alongside the project.

| File | Location | Contents |
|------|----------|----------|
| `design-tokens.md` | `.design/` | Color palette with hex values and rationale sentences, type scale (8 tokens), spacing scale, border radius tokens, shadow levels |
| `component-suggestions.md` | `.design/` | Three-layer component hierarchy, ASCII wireframe layouts per core flow, interaction pattern specs for every UI state |
| `verification-report.md` | `.design/` | Six-check audit table with ✅/⚠️/❌ statuses, contrast ratios, touch target results, remediation priorities |
| `DESIGN_BRIEF.md` | Project root | Canonical handoff document — app context, design philosophy, inspiration references, full token and component spec, verification summary, decision rationale, next-step checklists |

---

## Design Decisions

**Front-load all agent file reads before spawning any sub-agent.**
The orchestrator reads all five agent prompt files in a single pass at startup. One round of file I/O up front; no repeated reads between phases. The tradeoff is a slightly higher startup cost. The gain is that the orchestrator has the full pipeline structure in scope before making any routing decisions — it reasons about sequencing from the start, not phase by phase.

**Pass the Project Brief to every downstream agent as a verbatim structured block.**
The Project Brief flows from Phase 1 into Phases 2, 3, 4, and 5 directly — not through a chain of summaries. Injecting a 200-token structured block is not the same as injecting a 3,000-token conversation history. The tradeoff is slightly more input token cost per downstream agent. The gain is that no agent can drift from the original genre classification, tone keywords, or design problem statement through paraphrase accumulation.

**Mandate the Verifier as a blocking phase before the Amplifier runs.**
The Verifier runs between generation and synthesis. The Amplifier receives the verification report and must incorporate open items into the final brief. The tradeoff is pipeline latency — five sequential phases take longer than three. But there is a critical boundary here: failures that reach the Amplifier get crystallized into institutional memory. Future developers implement from the brief. The check has to happen while it can still block the output.

---

## Known Limitations

- The pipeline is strictly sequential. No phases run in parallel. On large codebases, total wall-clock time can be significant.
- The Verifier calculates contrast ratios by approximation from hex values. It does not run a programmatic contrast checker against rendered output. "Approximate" means what it says.
- The Inspiration agent depends on Mobbin MCP (paid subscription required) or WebSearch. Both require network access. Offline use produces no inspiration references.
- The skill reads the project directory but does not execute the project. It cannot observe runtime behavior, actual rendered components, or existing visual regressions.
- There is no incremental mode. Each invocation runs the full five-phase pipeline from scratch.
- `DESIGN_BRIEF.md` is overwritten on each run. Previous runs are not versioned by the skill itself.
- The orchestration logic lives in natural language — there is no retry logic, no failure recovery. If an agent produces malformed output, the pipeline does not self-correct.
- The Verifier's touch target check is conditional on the Project Brief's platform classification. If the Thinking Partner misclassifies a mobile app as web, touch target checks are skipped.

---

## What's Next

- Parallel inspiration queries — run Mobbin and WebSearch concurrently and merge results, reducing Phase 2 latency on slow MCP connections.
- Incremental re-runs — detect which phases have already produced output files and skip them unless `--force` is passed, enabling targeted re-runs of the Verifier or Amplifier after manual token edits.
- Token export targets — add a post-Amplifier step that converts `design-tokens.md` into a CSS custom properties file, a Tailwind config extension, or a React Native theme object based on the platform identified in the Project Brief.
