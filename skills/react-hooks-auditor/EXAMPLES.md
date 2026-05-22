# React Hooks Auditor — Examples

## Basic usage

```
Audit this hook: [paste or upload useProgram.ts]
```

```
Review and refactor this hook: [upload useFetchData.js]
```

```
Find all `any` in this file and replace with proper types.
```

---

## Realistic scenarios

### Scenario 1 — TypeScript hook, full audit

**User:**
> Audit this: [uploads `useProgram.ts`]

**Phase 0:** Detects `.ts` extension → TypeScript → skip to Phase 1.

**Phase 1 — Plan output:**

File Map:
- Hooks found: `useProgram`, `useProgramList`
- React APIs: `useState`, `useEffect`, `useCallback`, `useQuery`
- External deps: `react-query`, `react-router-dom`
- Types file: `types/program.types.ts` (exists)

Any Detection Table:
| # | Location | Original | Proposed Interface | Target File |
|---|---|---|---|---|
| 1 | useProgram params | `}: any)` | `UseProgramProps` | append to types/program.types.ts |
| 2 | location state | `}: any = useLocation()` | `LocationState` | append to types/program.types.ts |

Risk Scan (sample rows):
| # | Criterion | Severity | Result | Reason |
|---|---|---|---|---|
| 2 | React Query config | critical | 🔴 FAIL | `cacheTime: 5` = 5ms (bug, should be 300_000) |
| 5 | useEffect deps | critical | ⚠️ WARN | Missing `programId` in dep array |

→ Saves plan to `.claude/plans/refactor-useProgram.md`
→ Asks: "Plan correct? Start from which phase, or run all?"

**Phase 2:** Replaces all `any`, appends interfaces to `types/program.types.ts`.

**Phase 3:** Full 15-criteria Vercel audit, Quality Score, Top 3 quick wins.

**Phase 4 (after confirm):** Outputs:
- `.claude/plans/refactor-useProgram.md` (updated)
- `types/program.types.ts` (appended)
- `hooks/useProgram.ts` (refactored, with `// ✅ FIX #N:` comments)

---

### Scenario 2 — JavaScript hook

**User:**
> Review this: [uploads `useFetchData.js`]

**Phase 0:** Detects `.js` → displays option menu:

```
⚠️  Detected JavaScript file: useFetchData.js

[A] Convert to TypeScript first, then audit
[B] Audit as JavaScript (no types)
[C] Convert only, skip audit

Reply A / B / C to continue.
```

**User:** B

**Skill:** Proceeds with JS audit, uses JSDoc instead of TypeScript types, marks `[TS-only]` criteria in risk scan.

---

### Scenario 3 — User wants only type replacement

**User:**
> Find and replace all `any` in `useCart.ts` only, no full audit needed.

**Skill does:**
- Runs Phase 0 + Phase 1 (plan required) → saves plan file
- Jumps to Phase 2 only
- Asks confirm before writing

---

## Trigger phrases

| What you say | What happens |
|---|---|
| "audit this hook" | Full 4-phase workflow |
| "refactor this hook" | Phases 0 → 1 → confirm → 2,3,4 |
| "find any in this hook" | Phase 0 → 1 → Phase 2 focus |
| "convert sang TypeScript" | Phase 0 detects JS → Option C |
| "check vercel best practices" | Phase 0 → 1 → Phase 3 focus |
| Paste hook code without comment | Auto-triggers if custom hook detected |

---

## Output files

```
.claude/plans/refactor-useProgram.md   ← plan + audit results
types/program.types.ts                  ← new/appended interfaces
hooks/useProgram.ts                     ← refactored hook with FIX comments
```

Each fix in the refactored hook is annotated:
```ts
// ✅ FIX #2: cacheTime: 5 = 5ms (bug) → 300_000 (5 minutes)
cacheTime: 300_000,
```
