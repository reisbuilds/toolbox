# Project Showcase Skill

A Claude Code skill that analyzes any existing project and produces three portfolio-ready
artifacts: comprehensive documentation, a portfolio case study, and a Marp slide deck.

## Agents

| Phase | Agent | Output |
|-------|-------|--------|
| Analyst | Reads the project and builds a Project Profile | Internal context |
| Docs Writer | Comprehensive technical documentation | `showcase/DOCUMENTATION.md` |
| Case Study | Portfolio narrative: problem → process → solution → outcomes | `showcase/CASE_STUDY.md` |
| Deck Builder | Marp markdown slide deck | `showcase/DECK.md` |

## Output Files

```
showcase/
  DOCUMENTATION.md   Technical + usage docs (comprehensive README-level)
  CASE_STUDY.md      Portfolio case study narrative
  DECK.md            Marp slide deck — render to PDF or HTML
```

## Installation

### Global (available in all your projects)

```bash
cp -r .claude/skills/project-showcase ~/.claude/skills/
```

### Per-project

```bash
cp -r .claude/skills/project-showcase <your-project>/.claude/skills/
```

## Usage

Run from inside the project you want to showcase:

```bash
# Full project analysis
/project-showcase

# Scoped to a specific feature or flow
/project-showcase authentication flow
/project-showcase API design
/project-showcase the onboarding experience
```

The focus argument narrows the Analyst's deep-read and shapes the Case Study
and Deck toward that area, while still reading the broader project for context.

## Rendering the Deck

The deck is written in [Marp](https://marp.app/) markdown.

**VS Code:** Install the [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)
extension — preview and export directly from the editor.

**CLI:**
```bash
# Install once
npm install -g @marp-team/marp-cli

# Render to PDF
marp showcase/DECK.md --pdf -o showcase/deck.pdf

# Render to HTML (self-contained, shareable)
marp showcase/DECK.md -o showcase/deck.html
```
