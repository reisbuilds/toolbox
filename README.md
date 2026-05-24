# toolbox

A collection of reusable Claude Code skills for building, designing, and documenting
projects. Each skill is a multi-agent orchestrator — a puppeteer that sequences
specialized sub-agents, each owning one job in the pipeline.

---

## Skills

### `/design-lifecycle` — Design Lifecycle Orchestrator

Maps to the **Double Diamond design process**. Takes any project and produces a
complete design brief by running five agents in sequence — from raw project analysis
through to a polished design handoff document.

#### Agents

| # | Agent | Role | Phase |
|---|-------|------|-------|
| 1 | **Thinking Partner** | Reads the codebase, classifies app genre and platform, maps core user flows, defines tone keywords, frames the design problem | Discover & Define |
| 2 | **Inspiration** | Queries Mobbin MCP for real UI screenshots; falls back to WebSearch. Distills nav patterns, color direction, typography, layout, and CTA conventions from production apps in the same genre | Discover → Develop |
| 3 | **Maker** | Generates a full design token set (color palette, type scale, spacing, shadows) and a component specification with ASCII wireframe layouts for every core flow | Develop |
| 4 | **Verifier** | Checks UI state coverage, WCAG AA contrast ratios, touch target sizes, design system consistency, and component-to-flow alignment | Before Delivery |
| 5 | **Amplifier** | Synthesizes all prior outputs into a single canonical `DESIGN_BRIEF.md` — design philosophy, token reference, component spec, verification status, and rationale | Deliver & Operate |

#### Output Files

```
.design/
  design-tokens.md          Color palette, typography scale, spacing, shadows
  component-suggestions.md  Component hierarchy, screen layouts, interaction patterns
  verification-report.md    ✅/⚠️/❌ checks: states, contrast, coverage, consistency
DESIGN_BRIEF.md             Canonical design handoff — philosophy, tokens, components, rationale
```

#### Usage

```
/design-lifecycle
/design-lifecycle <focus area>
```

Examples:
```
/design-lifecycle
/design-lifecycle onboarding flow
/design-lifecycle checkout and payment
/design-lifecycle dashboard and analytics
```

#### Mobbin MCP Setup

The Inspiration agent uses [Mobbin](https://mobbin.com) to pull real UI screenshots
from production apps. Requires an active paid Mobbin plan. Falls back to WebSearch
automatically if not configured.

```bash
claude mcp add mobbin --transport http https://api.mobbin.com/mcp
npx -y mobbin-mcp auth
```

Full setup: [`.claude/skills/design-lifecycle/README.md`](.claude/skills/design-lifecycle/README.md)

---

### `/project-showcase` — Project Showcase Orchestrator

Takes any existing project and produces three portfolio-ready artifacts — comprehensive
documentation, a portfolio case study, and a Marp slide deck — by running four agents
in sequence.

#### Agents

| # | Agent | Job | Output |
|---|-------|-----|--------|
| 1 | **Analyst** | Reads the project: tech stack, architecture, features, interesting decisions, challenges, code quality signals. Produces a structured Project Profile passed to all subsequent agents | Internal context |
| 2 | **Docs Writer** | Writes comprehensive documentation: overview, architecture, setup, usage, design decisions, known limitations, and roadmap. Honest, scannable, developer-quality | `showcase/DOCUMENTATION.md` |
| 3 | **Case Study** | Writes a portfolio case study narrative: context → problem → process & decisions → solution → outcomes → reflection. First-person, specific, no buzzwords | `showcase/CASE_STUDY.md` |
| 4 | **Deck Builder** | Writes a Marp markdown slide deck (10–12 slides): cover, problem, solution, architecture, key decisions, challenges, outcomes, next steps, close. Renderable to PDF or HTML | `showcase/DECK.md` |

#### Output Files

```
showcase/
  DOCUMENTATION.md   Comprehensive technical and usage documentation
  CASE_STUDY.md      Portfolio case study: problem → process → solution → outcomes
  DECK.md            Marp slide deck — render to PDF or HTML with Marp CLI
```

#### Usage

```
/project-showcase
/project-showcase <focus area>
```

Examples:
```
/project-showcase
/project-showcase authentication flow
/project-showcase API design
/project-showcase the onboarding experience
```

#### Rendering the Deck

```bash
# Render to PDF
npx @marp-team/marp-cli showcase/DECK.md --pdf -o showcase/deck.pdf

# Render to self-contained HTML
npx @marp-team/marp-cli showcase/DECK.md -o showcase/deck.html
```

VS Code: install [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) for live preview and export.

Full setup: [`.claude/skills/project-showcase/README.md`](.claude/skills/project-showcase/README.md)

---

## Installing Skills

### All skills at once (global)

```bash
git clone https://github.com/reisbuilds/toolbox
cp -r toolbox/.claude/skills/. ~/.claude/skills/
```

### Individual skill (global — available in all projects)

```bash
cp -r .claude/skills/design-lifecycle ~/.claude/skills/
cp -r .claude/skills/project-showcase ~/.claude/skills/
```

### Individual skill (per-project)

```bash
cp -r .claude/skills/design-lifecycle <your-project>/.claude/skills/
cp -r .claude/skills/project-showcase <your-project>/.claude/skills/
```

Once installed globally, invoke any skill by name in any Claude Code session.

---

## Skill Reference

| Skill | Command | Agents | Primary Output |
|-------|---------|--------|----------------|
| Design Lifecycle | `/design-lifecycle` | 5 | `DESIGN_BRIEF.md` + `.design/` |
| Project Showcase | `/project-showcase` | 4 | `showcase/` (docs, case study, deck) |
