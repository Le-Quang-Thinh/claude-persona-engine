# code-reviewer

Expert AI code reviewer. Analyzes any code file across 5 dimensions and produces a severity-tagged Markdown report.

## What it does

| Dimension | Checks |
|-----------|--------|
| Code Quality | smells, naming, structure, duplication |
| Bug Detection | logic errors, edge cases, null/undefined, stale closures |
| Security | XSS, injection, hardcoded secrets, auth gaps |
| Performance | unnecessary re-renders, memory leaks, missing memoization |
| Best Practices | error handling, types, testability |

Each issue is tagged: `🔴 Critical` · `🟠 Major` · `🟡 Minor` · `🔵 Suggestion`

## Install

```bash
cp -R skills/code-reviewer /your-project/.claude/skills/
cp skills/code-reviewer/command.md /your-project/.claude/commands/review.md
```

## Usage

### Via slash command
```
/review src/utils/format.ts
/review src/api/auth.ts --security
/review src/hooks/useProgram.ts --quick
/review src/utils/format.ts --fix
```

### Via natural language
- "review this code"
- "find bugs in this file"
- "check for security issues"
- "is this code good?"

## Flags

| Flag | Behavior |
|------|----------|
| `--quick` | Critical + Major only |
| `--security` | Security section deep-dive |
| `--perf` | Performance section deep-dive |
| `--fix` | Review + generate corrected file with `// ✅ FIX` comments |
| `--output` | Save report to `[filename].review.md` |
| `--react` | Force-load React hooks rules |
| `--lang go` | Override language detection |

## Skill routing

This skill detects when another skill is more appropriate and suggests a handoff:

- React hook + needs full refactor → suggests `react-hooks-auditor`
- Tests needed → suggests `unit-test-writer`
- Doc/spec file → suggests `doc-reviewer`

## Reference files

- `references/react-hooks-rules.md` — loaded automatically for React/TypeScript files
- `references/common-antipatterns.md` — loaded for Python, Go, SQL, and other languages
