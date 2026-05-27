---
marp: true
theme: gaia
paginate: true
---

<!-- _class: lead invert -->

# design-lifecycle
## One command. Full Double Diamond. Four files.

---

## Design Is a Sequence. Treat It Like One.

A single prompt cannot hold four distinct cognitive modes at once.

The result: output that sounds authoritative and is structurally broken.

- Wrong contrast ratios
- Missing UI states
- Tokens that don't match the components referencing them

Speed without standards is entropy.

---

## The Double Diamond Is Not Aesthetic. It's Structural.

Each phase demands different context, different tools, different output format.

- **Discover:** What problem are we actually solving?
- **Define:** What constraints are real?
- **Develop:** What does the solution look like in tokens and components?
- **Deliver:** Does it hold up before it becomes the standard?

Collapsing phases doesn't save time. It borrows it from whoever implements the design.

---

## The Architecture

SKILL.md (orchestrator) sequences five agents in order and commits four files to disk.

| Phase | Agent | Output |
|---|---|---|
| Discover & Define | Thinking Partner | Project Brief |
| Discover → Develop | Inspiration | Inspiration Summary |
| Develop | Maker | design-tokens.md, component-suggestions.md |
| Before Delivery | Verifier | verification-report.md |
| Deliver | Amplifier | DESIGN_BRIEF.md |

One orchestrator. No content generation at the top level. That separation is the entire design.

---

## The Orchestrator Does One Thing

SKILL.md sequences. It does not generate.

- Reads all five agent prompts up-front before spawning anything
- Extracts named structured blocks from each agent's output
- Injects only what the next agent needs — not the full conversation history
- Displays a live checklist so the user sees exactly where the pipeline is

Token counts stay manageable. Agents stay focused. The orchestrator stays out of the way.

---

## Data Contracts Are the Connective Tissue

Agents need clean, typed inputs. Not a transcript of upstream reasoning.

**Project Brief** — extracted after the Thinking Partner, injected into every downstream agent. Prevents drift across the full pipeline.

**Inspiration Summary** — extracted after Inspiration, injected only into Maker and Amplifier. The right context at the right phase.

These two blocks are the architecture. Everything else is implementation.

---

<!-- _class: lead invert -->

## The Verifier Is Not Optional

Most pipelines: generate → deliver.

This one: generate → **verify** → deliver.

Six checks. Hard thresholds.

- WCAG AA contrast ratios on every text-on-background pair in the token palette
- Touch targets: 44×44pt iOS, 48×48dp Android
- Token consistency cross-referenced against every component referencing them
- Component and flow coverage against the Project Brief

Failures caught here get fixed. Failures that reach DESIGN_BRIEF.md become the implementation standard.

---

## What You Get

Four files. Committed to disk. Versioned. Survive the conversation.

| File | What it is |
|---|---|
| `design-tokens.md` | Color, spacing, and type tokens with values and usage |
| `component-suggestions.md` | Component inventory mapped to design tokens |
| `verification-report.md` | Six-check quality gate with pass/fail per criterion |
| `DESIGN_BRIEF.md` | Final synthesized brief, ready to hand to an implementer |

---

## What Was Hard

The data contract problem was solved too late.

Deciding what to extract as a typed structured block versus what to leave as prose — that decision shaped every agent prompt. The schemas for Project Brief and Inspiration Summary should have been defined before any agent was written.

Define your data contracts first. The prompts become easier. The extraction becomes reliable. The pipeline becomes predictable.

---

## What's Next

- **Schema validation at extraction time** — catch malformed Project Brief or Inspiration Summary before they propagate downstream
- **Incremental re-runs** — re-invoke a single agent with an updated brief without restarting the full pipeline
- **Human-in-the-loop pause points** — surface the Project Brief to the user for approval before the Maker begins, making the definition phase collaborative

---

<!-- _class: lead invert -->

# design-lifecycle

One command. Five agents. Four files.
Full Double Diamond from your terminal.

`.claude/skills/design-lifecycle/`
