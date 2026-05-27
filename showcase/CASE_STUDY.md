# design-lifecycle — Case Study

**Role:** Solo
**Status:** MVP

---

## TL;DR

- Single-prompt design generation produces internally coherent but cross-phase incoherent output — tokens that don't reference the inspiration, components that don't map to identified flows — because no single context window can hold discovery, research, specification, verification, and synthesis simultaneously without collapse.
- I built a five-agent orchestration pipeline that sequences those tasks serially, extracts named structured blocks from each agent's output, and re-injects them into downstream agents as explicit data contracts — not conversation history.
- The orchestrator reads all five agent files before spawning any agent, giving it the full pipeline in scope before execution begins. It carries zero design domain knowledge. All domain knowledge lives in the agents.
- The result is four files with specific, actionable values: hex values with rationale sentences, a component list mapped to actual user flows, a six-check verification report, and a canonical DESIGN_BRIEF.md written for humans to implement from.

---

## Context — The Situation

Most developers who need design direction do one of three things: hire a freelancer, copy an app that looks close enough, or ask an AI to generate a spec in one shot. The first is expensive and slow. The second produces derivative work with no reasoning behind it. The third produces output that sounds authoritative but falls apart the moment you try to build from it — contrast ratios that don't clear WCAG AA, empty states described in prose but absent from every layout, component names that reference tokens never defined.

The developer who reaches for this skill is not looking for aesthetic inspiration. They have a codebase. They need a token system, a component list, and a design brief they can hand to themselves or a contractor and actually implement. They are in the moment of "I know what I'm building but I have no idea where to start with color, typography, or component structure." That moment is specific. Most tools either miss it entirely or respond to it with vague guidance dressed up as a system.

---

## Goals

**Functional goals:**
- Classify the application from codebase signals before generating any design recommendations
- Source real-world UI references from actual products in the same genre, not generic design advice
- Generate concrete token values — hex codes, type scale numbers, spacing increments — with rationale sentences for each decision
- Verify the output against six discrete checks before synthesis
- Produce a canonical handoff artifact that names open items rather than hiding them

**Quality goals:**
- Every agent receives exactly what it needs — not the full conversation history, not a paraphrase
- The Verifier runs before the Amplifier, not after
- The orchestrator carries zero design domain knowledge — coordination layer only
- Output format per agent is an interface contract, not a suggestion

---

## The Problem

The failure mode I was trying to solve is not a model capability problem. It is a task architecture problem. When you write "generate a complete design system for this app" in a single prompt, you are asking the model to do five distinct things simultaneously: understand the codebase, research analogous products, specify tokens, specify components, and verify that the tokens and components are internally consistent and accessible. That is not one task. That is a pipeline.

The result of collapsing a pipeline into a prompt is predictable. Each section of output is generated with the full original context active and no feedback loop between sections. The model cannot inspect its own token definitions when writing component suggestions, because both are being written in the same completion. So you get color tokens that don't satisfy the contrast ratios cited two paragraphs later. Component suggestions that reference a "Primary-500" that was never defined. Error states described once in prose and then never again. The output feels complete because it is long. It is not complete.

But the deeper issue is context coherence across phases. Discovery requires reading and classifying — deliberately not generating solutions yet. Inspiration research requires searching and synthesizing external references — not producing specifications. Specification requires building on a fixed classification and fixed inspiration references — not re-deriving them from scratch. Verification requires checking the specification against an external standard — not authoring more design content. Synthesis requires assembling all of the above into a coherent document — not second-guessing the earlier phases. These are cognitively incompatible modes. Forcing one context window to inhabit all five simultaneously is why single-prompt design generation produces cross-phase incoherence even when each individual section reads well.

---

## Process & Decisions

### The Orchestration Layer

I could have written one very long prompt. I chose not to, because prompt length is not the same as task focus. A 5,000-word prompt asking for all five phases at once is still one completion — every section is generated with the full original context active and no structured handoff between them.

A pipeline orchestrator changes the information flow. SKILL.md is a pure coordination layer: it reads all five agent files in a pre-pass, then sequences agents serially — extracting named structured blocks from each output and re-injecting them into downstream agents. It does not generate design content. It does not interpret design decisions. It sequences and routes. All domain knowledge lives in the five agent files.

The cost is real: five sequential sub-agent calls take longer than one completion. But there is a critical boundary here. The pipeline does not just produce faster output — it produces output where each phase actually uses the previous phase's result as a grounded input rather than as context it might drift away from. That distinction is worth the latency.

### The Project Brief as Data Contract

The Project Brief is the central data contract of the pipeline. The Thinking Partner produces it in Phase 1 with required named fields: genre, platform, maturity, core user flows, tone keywords, design problem statement. The orchestrator extracts that block verbatim and re-injects it — labeled, unchanged — into Phases 3, 4, and 5.

This matters for a specific reason. The Verifier audits against the Project Brief, not against the Maker's design recommendations in isolation. Without the Project Brief flowing downstream, the Verifier would be checking whether the design is internally self-consistent. With it, the Verifier checks whether the design is consistent with what the application actually is. Those are different audits with different failure modes.

The fragility is real: extraction depends on agents producing exact heading names and block structure. If the Thinking Partner drifts from its required format, downstream agents receive an incomplete contract and the quality signal breaks. Every agent's output format is specified as an interface, not documentation. That is the only enforcement mechanism available in a natural-language orchestration system.

### The Verifier's Placement

Most pipelines go: generate → deliver. I placed the Verifier between the Maker and the Amplifier because the alternative is crystallizing failures into institutional memory.

The Verifier runs six discrete checks: UI state coverage per flow, approximate WCAG AA contrast ratios, touch target minimums, design system internal consistency, component coverage versus identified flows, and alignment between token decisions and Inspiration Summary recommendations. Each check produces a pass, warn, or fail signal. The Amplifier receives the full verification report and must surface open items in `DESIGN_BRIEF.md` rather than silently inheriting unchecked problems.

If the Amplifier ran first and the Verifier ran after, the failures would appear as an appendix. Future developers implement from the brief. The check has to happen while it can still block the output, not after the output has already been written.

---

## The Solution

You run `/design-lifecycle` in any project directory. The orchestrator reads all five agent files in one pass before spawning anything. It then sequences the pipeline: Thinking Partner classifies the codebase and produces the Project Brief. Inspiration sources real-world products in the same genre and produces the Inspiration Summary. Maker builds `design-tokens.md` — hex values with rationale sentences, type scale, spacing system, border radius, elevation — and `component-suggestions.md`, a prioritized component list mapped to the user flows identified in Phase 1. Verifier runs six discrete checks and produces a pass/warn/fail report. Amplifier synthesizes everything into `DESIGN_BRIEF.md` — written for humans, not for the pipeline, with explicit tone rules baked into its prompt: no meta-commentary, specific values not adjectives, no hedging.

The architecture is deliberate about what the orchestrator is not. It carries no design knowledge. It does not interpret design decisions or evaluate design quality. It reads agent files, sequences agents, extracts named blocks, and injects them into downstream agents. Individual agents are replaceable without touching the orchestrator. The coordination layer is stable because it is empty of domain knowledge.

---

## Outcomes & Signals

The output contract is what makes the pipeline reliable in practice. Every agent's required output format is specified with exact heading names and block structure — this is interface design, not documentation. The orchestrator can extract and re-inject reliably because the format is enforced at the agent level, not assumed at the orchestrator level.

The Verifier's six-check structure creates a graduated quality signal rather than a binary pass/fail. A fail on WCAG contrast is different from a warn on component coverage, which is different from a pass on internal consistency. The Amplifier can name those distinctions in `DESIGN_BRIEF.md` so the developer knows exactly what requires remediation before implementation versus what is advisory.

The Amplifier's explicit tone rules — no meta-commentary, specific values not adjectives, no hedging — address LLM default failure modes directly. They are not stylistic preferences. They correct for the specific ways language models degrade handoff artifacts: by describing their own process, by using adjectives where values should appear, by qualifying every recommendation. The rules exist because those failure modes are predictable and correctable at the prompt level.

This is MVP. The pipeline works well for greenfield projects and applications at early design maturity. It is less useful when a mature design system already exists — the token specifications will conflict with established conventions rather than extend them.

---

## What I'd Do Differently

The data contract definition was the hardest problem in the project, and I solved it too late. I spent time iterating on individual agent prompt quality before I had nailed down exactly what fields the Project Brief needed, exactly what structure the Inspiration Summary would follow, and exactly how those blocks would be extracted and re-injected. Getting the contracts right first would have made the agent prompts easier to write — because each agent's output format would have been pre-defined by the contract requirements, not negotiated during iteration. I worked from the inside out when I should have worked from the outside in.

I would also invest earlier in making the output format requirements failure-resistant. Right now, the enforcement mechanism is asking agents to produce exact heading names and block structure in natural language. That works until it doesn't — and when it fails, the failure is silent. A structured output schema, even a simple one, would catch format drift before extraction and give the orchestrator something to respond to rather than silently propagating a broken contract downstream.

---

## What's Next

- **Structured output contracts.** Replace heading-based extraction with typed JSON blocks in each agent's required output — making the data contract machine-readable and detectable when malformed, rather than pattern-matched from prose.
- **Verifier as a blocking gate.** A fail-level finding should stop the pipeline before the Amplifier runs, surfacing the error explicitly rather than passing it to synthesis.
- **Incremental re-runs.** Re-run from a specific phase — regenerate only the Maker output, for example, after manually editing token values — without re-running Thinking Partner and Inspiration from scratch.
