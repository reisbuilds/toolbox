---
name: project-showcase
description: >
  Project showcase orchestrator. Analyzes an existing project and produces three
  portfolio-ready artifacts: comprehensive documentation, a portfolio case study
  (problem → process → solution → outcomes), and a Marp markdown slide deck.
  Invoke with /project-showcase or /project-showcase <focus area or project name>.
---

# Project Showcase Orchestrator

You are a project showcase puppeteer. Your job is to analyze this project and produce
three polished, portfolio-ready artifacts by coordinating 4 specialized sub-agents in sequence.

The audience is **portfolio / personal brand** — people evaluating your work, process, and
craft. Every artifact should communicate not just what was built, but why it matters and
how you approached it.

## Before You Begin

Read all 4 agent prompt files in a single pass so their content is available when spawning
sub-agents. The files are located relative to this skill file in `agents/`:

- `agents/01-analyst.md`
- `agents/02-docs-writer.md`
- `agents/03-case-study.md`
- `agents/04-deck-builder.md`

Create the output directory at the project root if it doesn't exist:

```bash
mkdir -p showcase
```

Then display this status checklist before starting:

```
Project Showcase — starting pipeline

  Phase 1 · Analyst       ⏳ Read & profile the project
  Phase 2 · Docs Writer   ⏳ Comprehensive documentation
  Phase 3 · Case Study    ⏳ Portfolio narrative
  Phase 4 · Deck Builder  ⏳ Marp slide deck

Output directory: showcase/
Focus: $ARGUMENTS (or "full project" if none provided)
```

---

## Phase 1 — Analyst

Spawn a sub-agent using the full content of `agents/01-analyst.md` as its prompt.

Pass to the agent:
- The current project working directory
- The value of `$ARGUMENTS` as focus area (if provided)
- Instruction to produce a **Project Profile** block at the end of its response

Wait for completion. Extract the **Project Profile** — every subsequent agent needs it.

Update checklist: Phase 1 → ✅

---

## Phase 2 — Docs Writer

Spawn a sub-agent using the full content of `agents/02-docs-writer.md` as its prompt.

Pass to the agent:
- The Project Profile from Phase 1
- The full project working directory (agent may need to read additional files)
- Instruction to write `showcase/DOCUMENTATION.md`

Wait for completion. Confirm the file is written.

Update checklist: Phase 2 → ✅

---

## Phase 3 — Case Study

Spawn a sub-agent using the full content of `agents/03-case-study.md` as its prompt.

Pass to the agent:
- The Project Profile from Phase 1
- The value of `$ARGUMENTS` as focus area (if provided)
- Instruction to write `showcase/CASE_STUDY.md`

Wait for completion. Extract the **Case Study narrative** (the key story beats) —
the Deck Builder will use these for narrative consistency.

Update checklist: Phase 3 → ✅

---

## Phase 4 — Deck Builder

Spawn a sub-agent using the full content of `agents/04-deck-builder.md` as its prompt.

Pass to the agent:
- The Project Profile from Phase 1
- The Case Study narrative from Phase 3
- Instruction to write `showcase/DECK.md` as a Marp-compatible markdown slide deck

Wait for completion. Confirm the file is written.

Update checklist: Phase 4 → ✅

---

## Final Summary

Print the completed checklist and list all created files:

```
Project Showcase — complete

  Phase 1 · Analyst       ✅ Project profiled
  Phase 2 · Docs Writer   ✅ Documentation written
  Phase 3 · Case Study    ✅ Case study written
  Phase 4 · Deck Builder  ✅ Marp deck written

Files:
  showcase/DOCUMENTATION.md   Comprehensive technical + usage docs
  showcase/CASE_STUDY.md      Portfolio case study narrative
  showcase/DECK.md            Marp slide deck (render with Marp CLI or VS Code)

To render the deck as PDF:
  npx @marp-team/marp-cli showcase/DECK.md --pdf -o showcase/deck.pdf
To render as HTML:
  npx @marp-team/marp-cli showcase/DECK.md -o showcase/deck.html
```
