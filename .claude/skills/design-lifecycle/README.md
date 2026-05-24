# Design Lifecycle Skill

A Claude Code skill that orchestrates 5 specialized sub-agents through the Double Diamond
design process to produce a complete design brief for any project.

## Agents

| Phase | Agent | Role |
|-------|-------|------|
| Discover & Define | Thinking Partner | Analyzes the project and frames the design problem |
| Discover → Develop | Inspiration | Pulls real UI patterns from Mobbin or web search |
| Develop | Maker | Generates design tokens and component specifications |
| Before Delivery | Verifier | Validates coverage, contrast, and consistency |
| Deliver & Operate | Amplifier | Synthesizes everything into a canonical design brief |

## Output Files

Running this skill writes four files to your project:

```
.design/
  design-tokens.md          Color palette, typography, spacing, shadows
  component-suggestions.md  Component hierarchy, screen layouts, interaction patterns
  verification-report.md    Checklist: states, contrast, coverage, consistency
DESIGN_BRIEF.md             Canonical design handoff document
```

## Installation

### Global (available in all your projects)

```bash
cp -r .claude/skills/design-lifecycle ~/.claude/skills/
```

### Per-project

```bash
cp -r .claude/skills/design-lifecycle <your-project>/.claude/skills/
```

## Mobbin MCP Setup

The Inspiration agent uses [Mobbin](https://mobbin.com) to pull real UI screenshots from
production apps. Without it, the agent falls back to WebSearch — still useful, but Mobbin
gives richer, more curated references.

Mobbin MCP requires an active paid Mobbin plan.

**1. Add the MCP server:**
```bash
claude mcp add mobbin --transport http https://api.mobbin.com/mcp
```

**2. Authenticate:**
```bash
npx -y mobbin-mcp auth
```

**3. Verify in Claude Code:**
Type `/mcp` in any Claude Code session — `mobbin` should appear in the list.

Full docs: https://docs.mobbin.com/mcp/clients/claude-code

## Usage

```
/design-lifecycle
```

Or to focus on a specific area:

```
/design-lifecycle onboarding flow
/design-lifecycle checkout and payment
/design-lifecycle dashboard and analytics
```

The focus argument scopes the Thinking Partner's analysis and shapes the Maker's
component layouts to that area, while still reading the broader project for context.
