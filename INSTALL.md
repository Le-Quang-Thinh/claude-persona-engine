# Installation Guide

How to install these skills into any Claude Code project.

---

## Option A — Project-level (recommended for teams)

Skills installed at the project level are committed to git and shared with all team members.

### Install all skills

```bash
# From this repo root
cp -R skills/* /path/to/your-project/.claude/skills/

# Install slash commands
mkdir -p /path/to/your-project/.claude/commands
cp skills/react-hooks-auditor/command.md /path/to/your-project/.claude/commands/audit.md
cp skills/frontend-developer/command.md  /path/to/your-project/.claude/commands/fe.md
cp skills/code-reviewer/command.md       /path/to/your-project/.claude/commands/review.md
```

### Install a single skill

```bash
SKILL=code-reviewer   # change to: doc-reviewer | feature-planner | frontend-developer
                       #            react-hooks-auditor | react-karpathy-guidelines | unit-test-writer

mkdir -p /path/to/your-project/.claude/skills
cp -R skills/$SKILL /path/to/your-project/.claude/skills/

# If the skill has a command.md, install it too
cp skills/$SKILL/command.md /path/to/your-project/.claude/commands/$SKILL.md
```

### Verify installation

```
your-project/
└── .claude/
    ├── commands/
    │   ├── audit.md    ← /audit command
    │   ├── fe.md       ← /fe command
    │   └── review.md   ← /review command
    └── skills/
        ├── code-reviewer/
        ├── doc-reviewer/
        ├── feature-planner/
        ├── frontend-developer/
        ├── react-hooks-auditor/
        ├── react-karpathy-guidelines/
        └── unit-test-writer/
```

---

## Option B — User-level (global, applies to all your projects)

User-level skills live in `~/.claude/` and are available in every project you open.

```bash
# Install all skills globally
cp -R skills/* ~/.claude/skills/

# Install slash commands globally
mkdir -p ~/.claude/commands
cp skills/react-hooks-auditor/command.md ~/.claude/commands/audit.md
cp skills/frontend-developer/command.md  ~/.claude/commands/fe.md
cp skills/code-reviewer/command.md       ~/.claude/commands/review.md
```

> **Note:** User-level skills are private to your machine — they don't appear in the project's git repo.

---

## Option C — Selective (pick only what you need)

You don't have to install every skill. Common minimal setups:

### Code review only
```bash
TARGET=/path/to/your-project/.claude
cp -R skills/code-reviewer $TARGET/skills/
cp -R skills/react-karpathy-guidelines $TARGET/skills/  # recommended companion
cp skills/code-reviewer/command.md $TARGET/commands/review.md
```

### React hooks workflow (audit + tests)
```bash
TARGET=/path/to/your-project/.claude
cp -R skills/react-hooks-auditor $TARGET/skills/
cp -R skills/unit-test-writer $TARGET/skills/
cp skills/react-hooks-auditor/command.md $TARGET/commands/audit.md
```

### Frontend development
```bash
TARGET=/path/to/your-project/.claude
cp -R skills/frontend-developer $TARGET/skills/
cp -R skills/feature-planner $TARGET/skills/
cp -R skills/unit-test-writer $TARGET/skills/
cp skills/frontend-developer/command.md $TARGET/commands/fe.md
```

---

## After Installing

Open Claude Code in your project and verify:

```
/skills list
```

You should see the installed skills listed. Try a quick test:

```
/review src/utils/format.ts
```

---

## Customizing for Your Stack

Some skills have a `references/stack.md` file that contains project-specific config (versions, test runner, mock patterns). After installing, update it to match your project:

| Skill | File to customize | What to update |
|-------|------------------|----------------|
| `unit-test-writer` | `references/stack.md` | React version, testing library versions, mock patterns |
| `react-hooks-auditor` | `references/vercel-checklist.md` | Remove criteria not relevant to your stack |

---

## Uninstalling

```bash
# Remove a single skill
rm -rf /path/to/your-project/.claude/skills/code-reviewer
rm /path/to/your-project/.claude/commands/review.md

# Remove all skills
rm -rf /path/to/your-project/.claude/skills/
rm -rf /path/to/your-project/.claude/commands/
```
