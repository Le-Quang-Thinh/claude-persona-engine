# unit-test-writer

Generates complete, runnable unit tests for React hooks, components, and TypeScript utility functions. Uses Jest + @testing-library/react, following the React 18 + CRACO + Webpack stack.

## What it does

Runs in 3 phases:
1. **Analyze** — identifies code type, dependencies to mock, input/output, and any bugs found
2. **Plan test cases** — lists all cases grouped as Happy path ✅ / Edge case 🔶 / Error case ❌ (minimum 3+2+1)
3. **Generate** — writes complete test file with JSDoc block containing analysis, bugs found, coverage estimate, and run commands

Output includes a JSDoc header so the analysis is always visible when you open the test file.

## Install

```bash
cp -R skills/unit-test-writer /your-project/.claude/skills/
```

No slash command — triggered automatically when you ask for tests.

**After installing**, update `references/stack.md` to match your project's actual versions and mock patterns.

## Usage

- "write unit tests for useProgram.ts"
- "generate tests for this hook"
- "test this utility function"
- "mock the API call and test this"
- "how do I test this file?"

Or paste code without asking — if the context is testing, the skill activates automatically.

## Output format

```typescript
/**
 * @file useProgram.test.ts
 * @description Unit tests for useProgram
 *
 * --- ANALYSIS ---
 * Type: React hook
 * Returns: { data, isLoading, error, refetch }
 * Mocks: axios (network), react-query QueryClient
 *
 * --- BUGS FOUND ---
 * ⚠️  Line 42: cacheTime: 5 = 5ms (likely bug)
 *     Fix: cacheTime: 300_000
 *
 * --- COVERAGE ---
 * Estimated: ~85% — main paths + error states covered
 *
 * --- HOW TO RUN ---
 * yarn test useProgram.test.ts
 */
```

## Customizing for your stack

Edit `references/stack.md` to update:
- React version
- Testing library versions
- Test runner (`yarn test` vs `npm test` vs `npx jest`)
- Mock patterns (React Query version, Router version)
- Path aliases (`@hooks`, `@components`, etc.)

## Does NOT generate

- Integration tests
- End-to-end tests (Playwright, Cypress)
- Performance tests

## Reference files

- `references/stack.md` — project-specific versions and mock patterns **(customize this)**
- `references/test-rules.md` — naming conventions, AAA pattern, what to mock
