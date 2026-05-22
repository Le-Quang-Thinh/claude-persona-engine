# Karpathy Guidelines — Examples

These guidelines shape how Claude approaches coding tasks — they apply implicitly whenever this skill is active.

---

## Guideline 1 — Think Before Coding

**Without guidelines (bad):**
> User: "Add authentication to this app."
> Claude: *immediately writes 200 lines of JWT middleware*

**With guidelines (good):**
> "Before I start — a few assumptions I'm making:
> 1. You want session-based auth, not OAuth (no external provider mentioned)
> 2. The existing `/api` routes need to be protected
> 3. You're OK with bcrypt for password hashing
>
> Two approaches: (A) JWT stateless, (B) server sessions with Redis.
> Which fits your setup better?"

---

## Guideline 2 — Simplicity First

**Without guidelines (bad):**
> User: "Add a loading spinner to this button."
> Claude: creates `LoadingContext`, `useLoadingReducer`, `withLoading` HOC, `LOADING_STATES` enum

**With guidelines (good):**
> ```tsx
> <button disabled={isLoading}>
>   {isLoading ? <Spinner /> : 'Submit'}
> </button>
> ```
> One boolean prop. Done.

---

## Guideline 3 — Surgical Changes

**Without guidelines (bad):**
> User: "Fix the typo in the button label."
> Claude: reformats the entire file, renames variables, adds TypeScript types to unrelated props

**With guidelines (good):**
> Changes exactly: `"Sbumit"` → `"Submit"` on line 42. Nothing else touched.

---

## Guideline 4 — Goal-Driven Execution

**Without guidelines (bad):**
> User: "Fix the login bug."
> Claude: guesses what might be wrong, changes several things at once

**With guidelines (good):**
> "Here's my plan before I touch anything:
> 1. Write a test that reproduces the login failure → verify: test fails
> 2. Fix the session token expiry check → verify: test passes
> 3. Run full auth test suite → verify: no regressions
>
> Starting with step 1."

---

## When these guidelines apply

These are behavioral rules, not a separate workflow. They activate whenever Claude is writing, editing, or reviewing code under this skill — shaping responses toward caution, minimalism, and verifiability over speed and speculation.
