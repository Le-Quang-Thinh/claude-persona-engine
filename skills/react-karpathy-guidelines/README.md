# react-karpathy-guidelines

Behavioral guidelines that reduce common LLM overcoding mistakes. Derived from Andrej Karpathy's observations on LLM coding pitfalls.

This skill is always-on — it is loaded by other skills as a companion to keep Claude disciplined during code generation.

## What it does

Enforces 4 rules:

1. **Think before coding** — surface assumptions, present tradeoffs, push back when something is unclear
2. **Simplicity first** — minimum code that solves the problem; no speculative features or abstractions
3. **Surgical changes** — touch only what must be touched; don't "improve" adjacent code
4. **Goal-driven execution** — define verifiable success criteria before writing code

## Install

```bash
cp -R skills/react-karpathy-guidelines /your-project/.claude/skills/
```

No slash command. Load it by referencing in another skill or CLAUDE.md:
```
Load @.claude/skills/react-karpathy-guidelines/SKILL.md before responding.
```

## Usage

Reference it in your CLAUDE.md or other skill commands to apply it globally:

```markdown
# CLAUDE.md
Always apply @.claude/skills/react-karpathy-guidelines/SKILL.md when writing code.
```

Or combine with another task:
- "refactor this hook following the Karpathy guidelines"
- "implement this feature, keep it simple"

## Note on skill name

The folder is named `react-karpathy-guidelines` but the `name` field in `SKILL.md` frontmatter is `karpathy-guidelines`. Claude uses the frontmatter `name` for skill routing — this is intentional (the skill is not React-specific).
