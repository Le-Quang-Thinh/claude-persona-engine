# feature-planner

Reads spec documents from a folder → analyzes → generates a detailed implementation plan → produces a Mermaid code flow diagram.

## What it does

Runs in 4 phases:
1. **Discovery** — scans folder, reads all docs, classifies by priority
2. **Overview plan** — presents high-level steps and asks for confirmation
3. **Detailed plan** — exports `[feature-name].plan.md` automatically
4. **Diagram** — exports `[feature-name].diagram.md` with Mermaid diagram

## Install

```bash
cp -R skills/feature-planner /your-project/.claude/skills/
```

No slash command — triggered by describing the task.

## Usage

Point Claude to a folder containing your spec files:

- "create a plan for the features in docs/auth-redesign/"
- "analyze the spec in specs/payment-flow/ and plan it out"
- "how should I implement this feature? — see docs/user-profile.md"
- "read the docs and plan it out"

## Tips

For best results, enable Opus Plan Mode before running:
```
/model opusplan
Shift+Tab → enable Plan Mode
```
Opus handles planning, Sonnet takes over during implementation.

## Output files

| File | Generated when |
|------|---------------|
| `[feature-name].plan.md` | Auto-exported after Phase 2 confirmation |
| `[feature-name].diagram.md` | Auto-exported after file list is confirmed |

## Reference files

- `references/plan-format.md` — full template for `*.plan.md` output
- `references/diagram-guide.md` — Mermaid diagram types and examples
