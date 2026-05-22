# /review — Code Review Command

Trigger a full structured code review on the specified file or pasted code.

## Usage

```
/review [file_path]             # Full review
/review                         # Review code pasted in next message
/review [file_path] --quick     # Critical + Major issues only
/review [file_path] --security  # Security-focused review only
/review [file_path] --perf      # Performance-focused review only
/review [file_path] --fix       # Review + generate fixed version of the file
/review [file_path] --output    # Save report to .review.md file
/review [file_path] --react     # Force React hooks rules
/review [file_path] --lang go   # Override language detection
```

## What this command does

Runs the `code-reviewer` skill — 5-dimension analysis:

| Dimension | Checks |
|-----------|--------|
| 🧹 Code Quality | smells, naming, structure, duplication |
| 🐛 Bug Detection | logic errors, edge cases, null handling, stale closures |
| 🔒 Security | XSS, injection, secrets, auth gaps |
| ⚡ Performance | re-renders, memory leaks, missing memoization |
| ✅ Best Practices | error handling, types, testability |

Each issue tagged: `🔴 Critical` · `🟠 Major` · `🟡 Minor` · `🔵 Suggestion`

## Skill Routing

The command auto-detects when a different skill is more appropriate:

| Detected condition | Suggested skill |
|-------------------|-----------------|
| React hook + needs full refactor | `react-hooks-auditor` |
| Needs unit tests generated | `unit-test-writer` |
| Doc/spec file submitted | `doc-reviewer` |

You'll be asked before any handoff happens.

## Flags

| Flag | Behavior |
|------|----------|
| `--quick` | Return only 🔴 Critical and 🟠 Major issues, skip summary table |
| `--security` | Deep-dive Security section only, skip other dimensions |
| `--perf` | Deep-dive Performance section only, skip other dimensions |
| `--fix` | After review, generate a corrected version of the file with inline `// ✅ FIX` comments |
| `--output [path]` | Save the full review report to `[path]` (default: `[filename].review.md`) |
| `--react` | Force load React hooks rules regardless of file type |
| `--lang [name]` | Override language detection: `--lang python`, `--lang go`, `--lang sql` |

## Output

- Line-referenced issues with fix suggestions
- Scorecard: X/5 per applicable dimension (N/A dimensions excluded from Overall)
- Overall score = average of applicable dimensions × 2
- Top 3 priorities + positive highlights

## Examples

```bash
/review src/hooks/useProgram.ts              # full review (may suggest react-hooks-auditor)
/review src/hooks/useProgram.ts --quick      # fast: Critical + Major only
/review src/api/auth.ts --security           # security deep-dive
/review src/utils/format.ts --fix            # review + output fixed file
/review src/utils/format.ts --output         # save report to format.ts.review.md
/review                                      # paste code in next message
```

## Notes

- TypeScript/React files → React hooks rules auto-applied
- Files > 300 lines → focused on highest-impact issues only
- Use `--quick` for pre-commit or CI contexts
- Use `--fix` when you want a ready-to-apply corrected file, not just suggestions
