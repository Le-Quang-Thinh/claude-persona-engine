# react-hooks-auditor

Audits and refactors React custom hooks (TypeScript or JavaScript) against the **Vercel 15-criteria best-practices checklist**. Replaces all `any` types with proper interfaces, scores quality 0–100, and exports a refactored file with annotated fixes.

## What it does

Runs in 4 mandatory phases:
1. **Plan** — maps all `any` usages + runs 15-criteria risk scan → exports `.claude/plans/refactor-[name].md`
2. **Types** — replaces every `any` with a proper interface, appends to `types/[name].types.ts`
3. **Risk audit** — full checklist table: PASS ✅ / WARN ⚠️ / FAIL 🔴, quality score 0–100
4. **Refactor** — exports refactored hook with `// ✅ FIX #N` comments at each change

Supports JavaScript files: detects JS, pauses, and offers 3 options — convert to TS then audit (A), audit as JS (B), or convert only (C).

## Install

```bash
cp -R skills/react-hooks-auditor /your-project/.claude/skills/
cp skills/react-hooks-auditor/command.md /your-project/.claude/commands/audit.md
```

## Usage

### Via slash command
```
/audit useProgram.ts
/audit useAuth.ts
/audit src/hooks/useCourse.ts
```

### Via natural language
- "audit this hook"
- "refactor useProgram.ts"
- "replace all `any` in useAuth.ts"
- "check this hook against Vercel best practices"
- "convert this JS hook to TypeScript"

## Output files

| File | Description |
|------|-------------|
| `.claude/plans/refactor-[name].md` | Plan + audit results (auto-created in Phase 1) |
| `types/[name].types.ts` | New interfaces (appended or created in Phase 2) |
| `hooks/[name].ts` | Refactored hook with `// ✅ FIX #N` comments (Phase 4) |

## Quality score

```
Score = (PASS×10 + WARN×5) / 150 × 100

90-100: Excellent
70-89:  Good
50-69:  Needs work
<50:    Critical issues
```

## Customizing for your stack

After installing, update `references/stack.md` if you use:
- react-query v4+ / @tanstack/react-query (checklist item #2 mentions v3-specific `cacheTime`)
- A different types directory (default: `types/`)

## Reference files

- `references/vercel-checklist.md` — 15 criteria with PASS/WARN/FAIL examples
- `references/output-format.md` — exact format for plan file and phase outputs
- `references/common-patterns.md` — patterns to recognize during scan (stale closures, duplicate state, etc.)
- `references/js-to-ts.md` — conversion guide for JavaScript hooks
