---
name: react-hooks-auditor
description: >
  Audit, review, and refactor React custom hooks written in TypeScript OR JavaScript. Automatically:
  (1) detect language — if JS, ask for a conversion option before doing anything else;
  (2) build a PLAN before taking any action — listing all `any` to fix, risks to review,
  and files to create/modify; (3) replace all `any` with proper interfaces and append them to
  existing types/[name].ts; (4) evaluate risks against a 15-criterion Vercel React Best Practices
  checklist; (5) output the refactored file + audit report.

  Trigger this skill WHENEVER the user:
  - uploads a .ts/.tsx/.js/.jsx file containing custom hooks and asks to "review", "audit", "optimize", or "refactor"
  - says "find any", "replace any", "fix types", "check hook", or "convert to typescript"
  - says "review best practices", "check vercel", "find risks", or "find bugs in hook"
  - pastes React hook code without further explanation — if the code contains custom hooks, trigger automatically
---

# React Hooks Auditor

This skill operates in **mandatory phases, in order**. Never skip Phase 0 or Phase 1.

---

## Phase 0 — DETECT LANGUAGE (run first, before everything else)

Determine the language of the input file based on extension or content:

| Signal | Language |
|--------|----------|
| Extension `.ts` / `.tsx` | TypeScript → **skip Phase 0, go directly to Phase 1** |
| Extension `.js` / `.jsx` | JavaScript → **stop and ask for option** |
| No extension, content has no type annotations | JavaScript → **stop and ask for option** |
| Content has `: string`, `interface`, `<T>`, `as Type` | TypeScript → **go directly to Phase 1** |

### If the file is JavaScript

Display the following message and **ask for 1 of 3 options** before doing anything else:

```
⚠️  JavaScript file detected: [filename]

This skill works best with TypeScript. How would you like to proceed?

  [A] Convert to TypeScript first, then audit
      → Output: [name].ts + types/[name].types.ts
      → Takes longer, most complete result

  [B] Audit as-is in JavaScript, no conversion
      → Output: refactored [name].js (no types)
      → Some TypeScript-only criteria will be skipped

  [C] Convert to TypeScript only, skip audit
      → Output: [name].ts with basic types
      → Fastest option, audit can be run later

Reply A / B / C to continue.
```

Wait for the user's selection. **Do not proceed** without a choice.

### Handling each option

**Option A — Convert then audit:**
1. Run JS→TS conversion (see `references/js-to-ts.md`)
2. Write the converted file as `[name].ts`
3. Continue to **Phase 1** using the converted TS file
4. In the plan file, note: `Language: JS → TS (converted)`
5. Final output includes both the converted `.ts` file and the refactored version

**Option B — Audit as JavaScript:**
1. Continue to **Phase 1** with the original JS file
2. In the Any Detection table: replace the "Suggested Interface" column with "Suggested JSDoc"
3. In the Risk Scan: mark `[TS-only]` for criteria that do not apply (criterion 9)
4. Output: refactored JS file with JSDoc comments instead of TypeScript types
5. In the plan file, note: `Language: JS (no conversion)`

**Option C — Convert only:**
1. Run JS→TS conversion (see `references/js-to-ts.md`)
2. Write `[name].ts`
3. Create `types/[name].types.ts` with basic interfaces
4. Notify completion, **do not run audit**
5. Suggest: "Run the audit on the generated `.ts` file if you want a full review"

---

## Phase 1 — BUILD PLAN (mandatory, always first)

Before writing any code or making any fix, **always build a plan** and show it to the user. The plan has 3 parts:

### 1A. File Map
Parse the input file and list:
- Names of custom hooks found
- React APIs used (useState, useEffect, useCallback, useMemo, useRef, useQuery, useMutation, etc.)
- External dependencies (lodash, immer, react-router, react-query, etc.)
- Type files currently imported from (to know where to append interfaces)

### 1B. Any Detection Table
Scan all `any` occurrences in the file and build a table:

| # | Location (hook/variable) | Original code | Suggested Interface | Target file |
|---|--------------------------|---------------|---------------------|-------------|
| 1 | useProgram params | `}: any)` | `UseProgramProps` | types/program.types.ts |
| 2 | useLocation state | `}: any = useLocation()` | `LocationState` | types/program.types.ts |
| ... | | | | |

Rules for filling the table:
- If the corresponding types file already exists (detected via import statements) → write "append to [file]"
- If not yet present → write "create types/[domain].types.ts"
- Name interfaces in PascalCase, reflecting the correct domain (avoid generic names like `Props1`)

### 1C. Risk Scan Table
Run all 15 Vercel checklist criteria (see references/vercel-checklist.md) and build a table:

| # | Criterion | Severity | Prediction | Brief reason |
|---|-----------|----------|------------|--------------|
| 1 | Single responsibility | critical | ⚠️ warn | useFetchApi mixes query + mutation |
| 2 | React Query config | critical | 🔴 fail | cacheTime: 5 = 5ms |
| ... | | | | |

### 1D. Write the plan file

After building all 3 parts, **always write the plan to a file** before asking for confirmation:

```
.claude/plans/refactor-[filename-without-extension].md
```

Example: input file `useProgram.ts` → write to `.claude/plans/refactor-useProgram.md`

If the `.claude/plans/` directory does not exist → create it automatically, no need to ask.

Use the standard format defined in `references/output-format.md#plan-file`.

After writing the file, notify:
> `✅ Plan saved at .claude/plans/refactor-[filename].md`

Then ask the user:
> "Does the plan look correct? Which phase would you like to start from, or should I run everything?"

Only continue to Phase 2+ after the user confirms (e.g., "go ahead", "ok", "looks good").

---

## Phase 2 — REPLACE ANY → INTERFACES

For each row in the Any Detection table (Phase 1B):

1. **Generate interface** — infer the shape from how the variable is used in code, do not guess
2. **Check target file** — if the types file already exists, append; otherwise, create it
3. **Update import** in the original file — add an import for the new interface
4. **Replace** `any` with the corresponding interface

Key rules:
- Use `unknown` instead of `any` for fields whose shape is truly unknown
- Use generics (`T extends object`) instead of `any` for utility functions
- For union types, use explicit `TypeA | TypeB` instead of `any`
- Do not create overly broad interfaces (`[key: string]: unknown`) unless truly necessary
- Repeated magic strings → extract into `const enum` or `as const` object

Phase 2 output:
- `types/[name].types.ts` appended to or newly created
- Original hook file with all `any` replaced

---

## Phase 3 — RISK AUDIT (Vercel Best Practices)

Read references/vercel-checklist.md for the full 15 criteria.

For each criterion, evaluate:
- **✅ PASS** — code follows the rule correctly
- **⚠️ WARN** — issue exists but won't cause an immediate bug; should be fixed
- **🔴 FAIL** — actual bug or dangerous pattern; must be fixed

Phase 3 output is the full results table (see format in references/output-format.md).

After the table, calculate the **Quality Score**:
```
score = (PASS * 10 + WARN * 5) / (total * 10) * 100
```

Then list the **Top 3 quick wins** — the easiest fixes that yield the highest score improvement.

---

## Phase 4 — REFACTOR & OUTPUT FILES

Only run Phase 4 after the user confirms they want to proceed with the refactor.

Create the following files:
1. `.claude/plans/refactor-[name].md` — plan created in Phase 1, **updated** with actual audit results
2. `types/[name].types.ts` — new interfaces (if not already created in Phase 2)
3. `hooks/[name].ts` — refactored hook file, with `// ✅ FIX #N: [description]` comments at each change

In the refactored file, every change must include an explanatory comment:
```ts
// ✅ FIX #4: cacheTime: 5 = 5ms (bug) → 300_000 (5 minutes)
cacheTime: 300_000,
```

---

## General Rules

- **Never skip Phase 0** — always detect language first; ask for an option if JS
- **Never skip Phase 1** — even if the user says "just do it quickly" or "fix everything"
- **Never refactor without a confirmed plan**
- **Preserve business logic** — only fix type safety and React patterns; do not rewrite logic
- **Do not delete code** unless it is clearly duplicated
- If unsure about an interface shape → add a comment `// TODO: verify shape with backend`
- If a types file does not exist in the project → create one, named after the hook's domain
- For JS Option B: use JSDoc (`/** @param {string} id */`) instead of TypeScript types

---

## References

Read the following files when needed:
- `references/vercel-checklist.md` — full 15 evaluation criteria with examples
- `references/output-format.md` — standard format for plan tables, checklists, and reports
- `references/common-patterns.md` — common React hook patterns to recognize
- `references/js-to-ts.md` — guide for converting JS → TS for React hooks (read for Option A or C)
