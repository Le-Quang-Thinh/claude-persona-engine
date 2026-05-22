# Claude Code Skills

A collection of reusable Claude Code skills for React/TypeScript projects. Each skill is a self-contained module — drop any folder into your project's `.claude/skills/` directory and it works immediately.

---

## Skills

| Skill | What it does | Has slash command |
|-------|-------------|:-----------------:|
| [code-reviewer](skills/code-reviewer/) | 5-dimension code review: quality, bugs, security, performance, best practices | `/review` |
| [doc-reviewer](skills/doc-reviewer/) | Review specs, architecture docs, and project plans (Plan mode + Spec mode) | — |
| [feature-planner](skills/feature-planner/) | Read spec docs → generate implementation plan + Mermaid diagram | — |
| [frontend-developer](skills/frontend-developer/) | Plan & implement UI features with type safety, a11y, performance checklist | `/fe` |
| [react-hooks-auditor](skills/react-hooks-auditor/) | Audit & refactor React custom hooks against 15 Vercel best-practice criteria | `/audit` |
| [react-karpathy-guidelines](skills/react-karpathy-guidelines/) | Behavioral guidelines to reduce LLM overcoding (always-on, loaded by other skills) | — |
| [unit-test-writer](skills/unit-test-writer/) | Generate complete unit tests for hooks, components, and utility functions | — |

---

## Quick Install — Single Skill

```bash
# Copy one skill into your project
cp -R skills/code-reviewer /your-project/.claude/skills/

# Copy its slash command (if it has one)
cp skills/code-reviewer/command.md /your-project/.claude/commands/review.md
```

## Quick Install — All Skills

```bash
# Copy all skills at once
cp -R skills/* /your-project/.claude/skills/

# Copy all slash commands
cp skills/react-hooks-auditor/command.md /your-project/.claude/commands/audit.md
cp skills/frontend-developer/command.md  /your-project/.claude/commands/fe.md
cp skills/code-reviewer/command.md       /your-project/.claude/commands/review.md
```

See [INSTALL.md](INSTALL.md) for the full installation guide, including user-level (global) setup and stack customization.

---

## Skill Routing

Skills are aware of each other. When you trigger one, it may suggest a more appropriate skill:

```
code-reviewer   ──→  react-hooks-auditor  (if hook needs full refactor)
code-reviewer   ──→  unit-test-writer     (if tests are needed)
code-reviewer   ──→  doc-reviewer         (if a spec/doc is submitted)
```

---

## Structure of Each Skill

```
skills/{skill-name}/
├── SKILL.md          ← Core instructions loaded by Claude
├── README.md         ← Human-readable guide (install + usage)
├── command.md        ← Slash command definition (if applicable)
└── references/       ← Supporting reference files (loaded on demand)
    └── *.md
```
