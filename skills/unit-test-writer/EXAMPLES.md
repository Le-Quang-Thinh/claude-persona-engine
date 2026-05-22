# Unit Test Writer — Examples

## Basic usage

```
Write unit tests for this hook:
[paste useDebounce.ts]
```

```
Generate tests for this utility function:
[paste formatCurrency.ts]
```

---

## Realistic scenarios

### Scenario 1 — React custom hook

**User:**
> Write tests for this:
> ```ts
> function useDebounce<T>(value: T, delay: number): T { ... }
> ```

**Skill output:**

Phase 0 analysis:
```
Type: React hook (generic utility)
Dependencies to mock: setTimeout (fake timer via jest.useFakeTimers)
Returns: debounced value of type T
Bugs found: None
```

Phase 1 test cases:
- ✅ returns initial value immediately
- ✅ returns updated value after delay
- ✅ does not update before delay expires
- 🔶 handles delay = 0
- 🔶 handles rapid value changes (debounce collapses them)
- ❌ handles negative delay

Phase 2 generates: `useDebounce.test.ts` with JSDoc header + all cases.

---

### Scenario 2 — Bug found during analysis

**User:**
> Test this: `useCallback(() => fetchData(id), [])` — missing `id` in deps

**Skill responds:**
> **BUGS FOUND**
> ⚠️  Line 12: `useCallback` missing `id` in dependency array → stale closure bug
>     Fix: Change `[]` to `[id]`

Tests are still generated, bug is documented in JSDoc and flagged in chat.

---

### Scenario 3 — Utility function with edge cases

**User:**
> Generate tests for `formatCurrency(amount: number, currency: string): string`

**Skill generates cases:**
- ✅ formats USD correctly → `$1,234.56`
- ✅ formats EUR correctly → `€1.234,56`
- 🔶 handles 0 → `$0.00`
- 🔶 handles negative amounts → `-$50.00`
- 🔶 handles very large numbers
- ❌ throws for NaN input
- ❌ throws for unknown currency code

---

## Trigger phrases

| What you say | What happens |
|---|---|
| "write tests for this" | Full 3-phase workflow |
| "create unit test for this hook" | Analyzes + generates test file |
| "how do I test this file?" | Generates test with explanation |
| "mock this" | Identifies mock strategy + generates |
| [paste code without comment] | If testing context → triggers skill |

---

## Output file

```
src/hooks/useDebounce.test.ts
```

Contains:
- JSDoc block with ANALYSIS, BUGS FOUND, COVERAGE, HOW TO RUN
- Full imports ready to run
- All test cases grouped by ✅ / 🔶 / ❌

```
yarn test useDebounce.test.ts
yarn test --watch
yarn test --coverage --watchAll=false
```
