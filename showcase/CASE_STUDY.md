# design-lifecycle — Case Study

**Role:** Solo
**Status:** MVP

---

## TL;DR

- Single slash command runs a five-agent pipeline through the Double Diamond and produces four file-system artifacts: design tokens, component suggestions, a verification report, and a final design brief.
- The pipeline solves a real failure mode: collapsing design into one prompt produces confident-sounding noise. Splitting it into five sequential cognitive tasks — research, inspiration, specification, verification, synthesis — produces something you can actually build from.
- A mandatory Verifier sits between generation and delivery and runs six discrete checks, including WCAG AA contrast ratios and UI state coverage across every core flow.
- The orchestrator front-loads all five agent files before spawning anything, then injects only the relevant prior outputs into each agent — not the full conversation history.

---

## Context

### The Situation

Most developers who need design direction do one of three things: hire a freelancer, copy an existing app until it looks close enough, or ask an AI to generate a UI spec in one shot and hope it holds together. The first is expensive and slow. The second produces derivative work with no reasoning behind it. The third produces output that sounds authoritative but falls apart under scrutiny — wrong contrast ratios, missing empty states, component names that don't map to anything in the token system.

Design is a sequence of decisions, not a single generation. When you ask one prompt to do all of it simultaneously, the model has to hold context framing, pattern research, specification writing, accessibility validation, and synthesis in the same completion. Something always gets dropped. Usually it's the unglamorous stuff — the error states, the degraded states, the "what happens when the API is slow" question that every real app has to answer.

### Goals

- Produce a complete, implementation-ready design brief from a single command invocation.
- Separate each cognitive task into an isolated agent with a defined input contract and a defined output contract.
- Make accessibility validation a structural requirement, not an afterthought.
- Generate four versioned file-system artifacts that persist and can be checked into the project.
- Surface failures visibly — the orchestrator shows ⚠️ and ❌ from the Verifier rather than burying them.
- Keep each agent focused on its task without drowning it in prior conversation history.

---

## The Problem

The failure mode I was trying to solve isn't a model capability problem. It's a task architecture problem. When you write "design a complete UI spec for this app" in a single prompt, you're asking the model to do six distinct things at once: understand the codebase, research analogous patterns, specify tokens, specify components, validate the spec against accessibility standards, and synthesize all of it into coherent guidance. That's not one task. That's a pipeline.

The result of collapsing a pipeline into a prompt is predictable. You get output that's internally consistent within each section but inconsistent across sections. Color tokens that don't actually satisfy the contrast ratios cited two paragraphs later. Component suggestions that reference a "Primary-500" that was never defined in the token table. Empty states described in prose but missing from every screen layout. The output feels complete because it's long. It isn't complete.

The deeper issue is context framing. In a single prompt, everything is generated in one shot — which means the model can't actually inspect its own token definitions when writing component suggestions, because both are being written simultaneously. In a pipeline, the Maker agent receives the token file as input. It knows what exists. The Verifier then reads both files and checks them against each other. That's a fundamentally different architecture, and it produces fundamentally different quality.

---

## Process & Decisions

### Orchestrator vs. Monolith

I could have written one very long prompt. I chose not to because prompt length is not the same as task focus. A 5,000-word prompt asking for all five phases at once is still one completion. Every section of output is generated with the full original context active and no feedback loop between sections.

A pipeline orchestrator changes the information flow. Each agent receives exactly what it needs: the Project Brief travels to every downstream agent to prevent drift from the original problem framing. The Inspiration Summary travels to the Maker and the Amplifier but not the Verifier — the Verifier only needs the spec files and the brief. This is selective context injection, and it keeps each agent focused on its specific cognitive task.

The cost is latency. Five sequential sub-agent calls take longer than one completion. For a design brief you're going to reference for weeks or months, that tradeoff is obvious.

### The Data Contract Problem

When I started, I didn't have a clear answer to: what exactly gets passed between agents? I had to define a typed data contract — specific named blocks with predictable structure — rather than passing raw prose or full conversation histories.

The Project Brief and Inspiration Summary are both structured blocks with fixed field names. The orchestrator extracts them verbatim from agent output and injects them verbatim into downstream agents. This matters for two reasons. First, it prevents interpretation drift — the Amplifier sees the same Project Brief that the Maker saw, not a paraphrase. Second, it keeps token counts manageable. Injecting a 200-token Project Brief block is not the same as injecting a 3,000-token conversation history. The latter buries the signal.

Defining what deserved to be a structured block versus what could stay as prose was genuinely the hardest design decision in this project. I landed on: extract anything that needs to be referenced by name or field in a downstream agent. Leave everything else as prose.

### Why the Verifier Exists

Most pipelines go: generate → deliver. I added a mandatory verification step between the Maker and the Amplifier because the alternative is crystallizing failures into the final brief.

The Verifier runs six discrete checks with exact thresholds. Check 2, for example, calculates approximate contrast ratios for every text-on-background combination defined in the token palette against WCAG AA minimums — 4.5:1 for normal text, 3:1 for large text and UI components. It's not asking "does this feel accessible?" It's asking "does Neutral-900 on Surface-background clear 4.5:1?" Those are different questions with different answers.

The verification has to happen before synthesis, not after. If the Amplifier writes the final DESIGN_BRIEF.md incorporating token values that fail contrast checks, those failures become institutional memory. Future developers will implement from the brief. The check needs to happen while it can still block the output, not as an appendix no one reads.

---

## The Solution

You run `/design-lifecycle` or `/design-lifecycle <focus area>` in any project directory. The orchestrator reads all five agent files up front — one I/O pass before anything is spawned — then displays a phase checklist and starts the pipeline. You watch the checklist tick through: Thinking Partner, Inspiration, Maker, Verifier, Amplifier. Each phase updates as it completes.

At the end, you have four files you didn't have before. `.design/design-tokens.md` contains a complete token system — color palette, type scale, spacing scale, elevation, border radius. `.design/component-suggestions.md` contains component specifications with state coverage for each core flow. `.design/verification-report.md` contains the six-check audit with pass/warning/fail status for every item. `DESIGN_BRIEF.md` at the project root is the synthesis document — written for humans, not for the pipeline, with Design Decisions & Rationale that explains the reasoning behind consequential choices so future contributors don't accidentally reverse them.

The architecture is a sequential pipeline with typed handoffs. The orchestrator acts as a pure coordination layer — it reads, it spawns, it extracts, it injects. It doesn't generate design content. Each agent owns one cognitive task and produces one structured output. The Thinking Partner reads the codebase and produces a Project Brief. The Inspiration agent finds reference patterns and produces an Inspiration Summary. The Maker specifies tokens and components. The Verifier audits both. The Amplifier synthesizes everything into the final artifact. No agent touches another agent's domain.

---

## Outcomes & Signals

The output contract is what makes this useful in practice. Every agent enforces an explicit block format — the Thinking Partner must produce a Project Brief with specific named fields, the Inspiration agent must produce an Inspiration Summary with specific named sections. This means the orchestrator can extract and re-inject reliably. It also means the output is predictably structured for the user.

The Verifier is the most valuable part of the pipeline and the part most likely to be skipped in a simpler implementation. Running six enumerated checks — state coverage, contrast ratios, touch targets, internal consistency, component coverage, and inspiration alignment — before generating the final brief catches the class of errors that look fine in prose but break in implementation.

The Amplifier's Design Decisions & Rationale section solves a problem I've seen repeatedly: teams implement a design system, then six months later someone changes a decision that looked arbitrary without understanding the reasoning. At least five documented decisions with tradeoffs give future contributors a fighting chance.

This is MVP. The pipeline produces a solid design brief for greenfield and emerging-maturity projects. It's less useful when a mature design system already exists — the token specification will conflict with what's already established rather than extend it.

### What I'd Do Differently

The data contract definition was the hardest problem and I solved it too late. I spent time iterating on agent prompt quality before I nailed down exactly what fields the Project Brief needed and exactly how the Inspiration Summary would be structured. Getting the contract right first would have made the agent prompts easier to write, because each agent's output format would have been pre-defined by the contract rather than negotiated.

I'd also build a smarter focus-area scoping mechanism. Right now, the `$ARGUMENTS` focus area is passed to each agent as a string, and each agent interprets it independently. That works but it's loose. A structured focus object — with explicit field for feature name, affected flows, and out-of-scope areas — would produce tighter, more coherent scoping across the five agents without relying on each agent to interpret the same string the same way.

---

## What's Next

- Add a Phase 0 — a lightweight pre-flight check that reads the project's existing design system (Tailwind config, theme files, CSS custom properties) and passes it to the Maker as a constraint set, so generated tokens extend rather than replace what's already there.
- Build a diff mode: run the pipeline against a specific feature branch and produce a design-delta report showing what changed relative to the baseline brief.
- Make the Verifier's contrast check precise — right now it calculates approximate ratios; integrating an actual WCAG contrast calculation against the hex values in the token file would eliminate the "approximate" qualifier and make failures unambiguous.
