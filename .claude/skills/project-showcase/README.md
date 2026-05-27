# Project Showcase Skill

A Claude Code skill that analyzes any existing project and produces three portfolio-ready
artifacts: comprehensive documentation, a portfolio case study, and a Marp slide deck.

Four agents. One orchestration layer. Each phase receives only what it needs from upstream —
not a transcript of prior reasoning.

## Agents

| Phase | Agent | Role | Output |
|-------|-------|------|--------|
| 1 | Thinking Partner | Reads the project deeply and frames the narrative | Project Profile (internal) |
| 2 | Maker | Produces documentation and case study from the Profile | `showcase/DOCUMENTATION.md`, `showcase/CASE_STUDY.md` |
| 3 | Validator | Checks accuracy, voice, completeness, and specificity | `showcase/VALIDATION_REPORT.md` |
| 4 | Amplifier | Amplifies the story into a presentation artifact | `showcase/DECK.md` |

## Output Files

```
showcase/
  DOCUMENTATION.md      Technical + usage docs (comprehensive README-level)
  CASE_STUDY.md         Portfolio case study narrative
  VALIDATION_REPORT.md  Quality gate findings
  DECK.md               Marp slide deck — render to PDF or HTML
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

The focus argument narrows the Thinking Partner's deep-read and shapes all downstream
artifacts toward that area, while still reading the broader project for context.

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
