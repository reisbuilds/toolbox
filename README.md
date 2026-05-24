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

## Installing Skills

### All skills (global)
```bash
cp -r .claude/skills/. ~/.claude/skills/
```

### Single skill (global)
```bash
cp -r .claude/skills/design-lifecycle ~/.claude/skills/
```

### Single skill (per-project)
```bash
cp -r .claude/skills/design-lifecycle <your-project>/.claude/skills/
```
