---
marp: true
theme: gaia
paginate: true
---

<!-- _class: lead invert -->

# toolbox
## Five agents. One coherent output. No designer required.

<!-- Note: The hook targets developers who've tried AI generation and got output that sounded right but didn't hold together across sections. -->

---

# The Problem

> AI can write a design brief — it just can't hold five cognitively incompatible modes in the same context window without losing coherence.

- Discovery, research, specification, verification, and synthesis each require a different epistemic posture — one reading, one searching, one building, one auditing, one assembling
- A single long prompt collapses all five into one completion: every token generates under the full original context, with no structured handoff and no way to correct drift between phases
- The result is output that reads well in isolation and breaks down across sections — contrast ratios that don't match the stated visual language, case study decisions that ignore the problem named in paragraph one

<!-- Note: This is a task architecture problem, not a model capability problem. The fix is structural. -->

---

# What Was Built

**toolbox** — two multi-agent orchestration pipelines that decompose AI generation tasks into discrete phases, enforcing structured handoffs between agents instead of letting context drift.

- `/design-lifecycle` produces a complete design brief: classified codebase genre, real-world UI references, hex token values with rationale, component hierarchy, and a six-check verification pass before synthesis
- `/project-showcase` produces portfolio-ready artifacts: comprehensive documentation, a case study with concrete decisions, and a Marp slide deck — validated before the deck is written
- Each pipeline extracts named structured blocks from every agent and re-injects them downstream as explicit, labeled data contracts
- Both pipelines run as Claude Code skills — one slash command in any project directory

<!-- Note: Both pipelines have been run on this codebase. The outputs you're reading were produced by the tools they describe. -->

---

# How It Works

| Layer | Component | Role |
|-------|-----------|------|
| Orchestration | `SKILL.md` per skill | Sequences agents, routes named output blocks, holds zero domain knowledge |
| Discovery | Thinking Partner | Reads codebase signals, classifies genre and maturity, produces the central data contract |
| Execution | Maker / Inspiration / Verifier | Domain-specific agents that consume the contract and produce artifacts |
| Quality Gate | Verifier / Validator | Runs discrete checks before synthesis — failures are named, not inherited |
| Synthesis | Amplifier | Assembles final output with explicit anti-AI-voice tone rules |

<!-- Note: The orchestrator is a pure coordination layer. Agents are individually replaceable because they are the sole holders of their domain knowledge. -->

---

# Decision 1: The Zero-Knowledge Orchestrator

**The question:** Should the orchestrator understand design or documentation, or just coordinate agents that do?

**The choice:** `SKILL.md` carries no domain knowledge — it sequences and routes named structured blocks, nothing else. All domain knowledge lives in agent files.

**Why it matters:** Coordination logic stays stable regardless of what any individual agent does, which means updating the Verifier's WCAG criteria or the Amplifier's tone rules requires editing exactly one file — not touching the orchestration layer.

<!-- Note: The cost is real: the orchestrator cannot detect or recover from malformed agent output. The benefit is a separation that survives iteration on any one agent without cascading changes. -->

---

# Decision 2: Pre-Reading All Agent Files at Startup

**The question:** When should the orchestrator learn what each downstream phase needs?

**The choice:** Read every agent file in a single pass before spawning any agent, giving the orchestrator the full pipeline in scope before execution begins.

**Why it matters:** The orchestrator knows Phase 5 needs both the Phase 1 Project Brief and the Phase 2 Inspiration Summary before Phase 1 has run — which makes output routing correct by construction rather than discovered ad hoc at each handoff.

<!-- Note: The tradeoff is a longer startup pass before any visible progress. Acceptable for a skill that runs infrequently on deliberate demand. -->

---

# Decision 3: Named Structured Output Contracts

**The question:** How do you pass information between agents without letting context drift or balloon?

**The choice:** Each agent produces a named structured block — Project Brief, Inspiration Summary, Project Profile — with required fields. The orchestrator extracts and re-injects these blocks verbatim, labeled, into downstream agents.

**Why it matters:** A downstream agent that receives exactly the fields it needs acts on them rather than re-deriving upstream reasoning — and the Verifier audits the design against the original Project Brief, not just against the Maker's output in isolation.

<!-- Note: Format drift fails silently — there is no schema validation. If the Thinking Partner drifts from its required heading names, the orchestrator extracts an empty block and the quality signal breaks downstream. Known fragility. -->

---

# What Was Hard

**Contract-first sequencing — the mistake that cascaded**
→ Individual agent prompt quality was iterated before interface contracts were locked. Every Thinking Partner output format change cascaded into rewrites of three downstream prompts. The correct order is to define and lock the data contracts first, then write agent prompts to fill them.

**Preventing cognitive mode bleeding between agents**
→ Each agent receives exactly what it needs — not the full conversation history. Discovery agents do not generate solutions. Verification agents do not author content. The mode boundary is enforced structurally, not through instruction.

**Making output human-readable, not AI-readable**
→ Amplifier agents carry explicit prohibited-pattern rules: no process meta-commentary, no adjectives where values belong, no hedging qualifiers. "Comfortable padding" becomes "16px padding." Named prohibited patterns work; vague tone guidance does not.

<!-- Note: All three challenges were encountered during the build, resolved, and documented in CASE_STUDY.md as concrete decisions rather than general lessons. -->

---

# Results

- The project ran both skills on its own codebase — `DESIGN_BRIEF.md` generated by `/design-lifecycle`, this documentation and deck generated by `/project-showcase`
- Validation Report passed all five checks with zero critical findings: accuracy, voice and tone, completeness, specificity, and deck readiness
- Zero-buzzword audit found no instances of "leveraged," "robust," "seamless," or "best practices" in either artifact
- `DESIGN_BRIEF.md` names specific CSS variable values, specific contrast ratios, and specific pixel dimensions — the anti-AI-voice rules are working
- The Verifier's six-check graduated structure (pass / warn / fail per check) gives developers actionable prioritization, not a binary result

<!-- Note: This is MVP. The self-referential validation pass is the strongest signal that output contracts are coherent — the pipeline can describe itself accurately. -->

---

# What's Next

- Add schema validation on named output blocks so format drift fails at extraction time rather than propagating a broken contract downstream — a lightweight JSON wrapper around required fields would catch this without changing the natural-language interface
- Build a retry path for warn-level and fail-level Verifier findings before the Amplifier runs, rather than surfacing open items in `DESIGN_BRIEF.md` for the developer to resolve manually
- Add a codebase-aware entry point for projects with existing design systems, where the Maker extends and audits current token conventions rather than generating a parallel system from scratch

<!-- Note: All three are grounded in known limitations documented in CASE_STUDY.md — the next concrete steps given what the MVP revealed, not aspirational features. -->

---

<!-- _class: lead invert -->

# toolbox

github.com/evreisberg/toolbox
