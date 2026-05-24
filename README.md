# toolbox

Reusable Claude Code skills for all projects.

## Skills

### design-lifecycle

A design lifecycle orchestrator that puppeteers 5 specialized agents through the
Double Diamond process — from project discovery to a full design brief.

**Agents:** Thinking Partner → Inspiration (Mobbin) → Maker → Verifier → Amplifier

**Outputs:** design tokens, component spec, verification report, and a canonical `DESIGN_BRIEF.md`

See [`.claude/skills/design-lifecycle/README.md`](.claude/skills/design-lifecycle/README.md)
for installation and Mobbin MCP setup.

**Usage:**
```
/design-lifecycle
/design-lifecycle <focus area>
```

### project-showcase

Analyzes an existing project and produces three portfolio-ready artifacts:
comprehensive documentation, a case study narrative, and a Marp slide deck.

**Agents:** Analyst → Docs Writer → Case Study → Deck Builder

**Outputs:** `showcase/DOCUMENTATION.md`, `showcase/CASE_STUDY.md`, `showcase/DECK.md`

See [`.claude/skills/project-showcase/README.md`](.claude/skills/project-showcase/README.md)
for installation and Marp rendering instructions.

**Usage:**
```
/project-showcase
/project-showcase <focus area>
```

## Installing Skills

### All skills (global)
```bash
cp -r .claude/skills/. ~/.claude/skills/
```

### Single skill (global)
```bash
cp -r .claude/skills/design-lifecycle ~/.claude/skills/
cp -r .claude/skills/project-showcase ~/.claude/skills/
```

### Single skill (per-project)
```bash
cp -r .claude/skills/design-lifecycle <your-project>/.claude/skills/
cp -r .claude/skills/project-showcase <your-project>/.claude/skills/
```
