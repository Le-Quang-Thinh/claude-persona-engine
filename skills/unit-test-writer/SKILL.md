---
name: unit-test-writer
description: >
  Automatically generate complete unit tests for React hooks, components, or TypeScript utility functions.
  Uses Jest + @testing-library/react, following the React 18 + CRACO + Webpack stack.
  Trigger WHENEVER the user wants to: write unit tests, generate tests for a hook/func/component,
  "write a test for this", "create unit test", "test this hook", "mock this",
  paste code and ask how to test it, or ask "how do I test this file".
  Also trigger when the user pastes code without asking explicitly — if the context is testing, use this skill.
  Do NOT use for integration tests, e2e tests, or performance tests.
---

# Unit Test Writer

Generate high-quality unit tests for React 18 + TypeScript + Jest codebases.

---

## Default stack

Read `references/stack.md` for exact versions and project-specific config.

---

## Phase 0 — Analyze input code

Read the code provided, identify:

1. **Code type** — hook / utility func / component / class
2. **Dependencies** — does it call an API, use timers, React Query, Redux, localStorage, React Router?
3. **Input/output** — what does it receive, what does it return, any side effects?
4. **Potential bugs** — wrong logic, missing edge cases, incorrect deps array in `useCallback`/`useEffect`

Present a short analysis:
```
Type: React hook
Dependencies to mock: setTimeout (fake timer), callback prop
Returns: { set, clear, reset, setValue, getRefTimeout, setRefTimeout }
Bugs found: [if any]
```

If the code is too ambiguous or lacks context → ask exactly one clarifying question before continuing.

---

## Phase 1 — Identify test cases

List all test cases to be written, grouped into 3 categories:

| Group | Symbol | Description |
|-------|--------|-------------|
| Happy path | ✅ | Valid input, main flow |
| Edge case | 🔶 | Null, undefined, 0, empty, boundary values |
| Error case | ❌ | Invalid input, exceptions, failing side effects |

**Minimum count:** 3 happy + 2 edge + 1 error (scale up with complexity).

Present the list before writing code — **do not ask for confirmation**, proceed directly to Phase 2.

---

## Phase 2 — Generate test file

Write a complete, runnable test file. Follow all rules in `references/test-rules.md`.

**Filename:** `[original-name].test.ts` or `.test.tsx`
**Location:** same directory as the source file

Required format — always open with a JSDoc block containing the full Phase 3 report:

```typescript
/**
 * @file [filename].test.ts
 * @description Unit tests for [HookName / FuncName]
 *
 * --- ANALYSIS ---
 * Type: [hook | component | utility func]
 * Returns: [what the hook/func returns]
 * Mocks: [what was mocked and why]
 *
 * --- BUGS FOUND ---
 * ⚠️  Line [N]: [problem description]
 *     Fix: [suggested fix]
 * (or "None found" if clean)
 *
 * --- COVERAGE ---
 * Estimated: ~[N]% — [brief reason]
 *
 * --- HOW TO RUN ---
 * yarn test [filename].test.ts
 * yarn test --watch
 * yarn test --coverage --watchAll=false
 */

import { renderHook, act } from '@testing-library/react'
import { ... } from './originalFile'

describe('[HookName / FuncName]', () => {

  beforeEach(() => { ... })
  afterEach(() => { ... })

  // ✅ Happy path
  it('[describes specific behavior]', () => { ... })

  // 🔶 Edge case
  it('[describes the edge case]', () => { ... })

  // ❌ Error case
  it('[describes the error scenario]', () => { ... })
})
```

**JSDoc sections — always include all 4:**

| Section | Content |
|---------|---------|
| `ANALYSIS` | Code type, mocks used, what the hook/func returns |
| `BUGS FOUND` | Line number + problem + fix. Write `None found` if clean |
| `COVERAGE` | Estimated % and brief reason |
| `HOW TO RUN` | Exact commands for this specific file |

---

## Phase 3 — Explain & notes

All Phase 3 output is written directly into the JSDoc block at the top of the test file — so the analysis, bugs, coverage estimate, and run commands are always visible when you open the file.

After generating, briefly summarize in chat:
- How many test cases were generated
- Any bugs found (repeat the key point)
- One-liner on estimated coverage

---

## Behavior Rules

| Rule | Description |
|------|-------------|
| Phase 0 | Always analyze before writing tests |
| Phase 1 | List cases, **no confirmation needed**, proceed immediately |
| Phase 2 | Generate complete test with all imports, ready to run |
| Phase 3 | Always explain mocks + how to run |
| Bugs | If a bug is found → call it out clearly, never skip |
| Language | Test case names and all content in English |
| File | Auto-name the file correctly, never ask |