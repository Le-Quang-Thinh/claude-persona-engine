# Code Reviewer — Examples

## Basic usage

```
Review this code: [paste any code block]
```

```
Find bugs in this function.
```

```
Is this code secure? [paste API handler]
```

---

## Realistic scenarios

### Scenario 1 — TypeScript utility function

**User:**
> Review this:
> ```ts
> function getUser(id: string) {
>   return fetch(`/api/users/${id}`)
>     .then(res => res.json())
> }
> ```

**Skill output:**

```
## 🔍 Code Review — TypeScript · getUser

**Assumption**: Utility function, no auth context visible
**Lines reviewed**: 4
**Issues found**: 🔴 1 critical · 🟠 1 major · 🟡 0 minor · 🔵 1 suggestion

### 🐛 Bug Detection
**🔴 Critical** `line 3` — No error handling for failed fetch
fetch() resolves even for 4xx/5xx responses. res.json() will succeed with error data.
> 💡 Fix: Check `if (!res.ok) throw new Error(res.statusText)` before .json()

### 🔒 Security
**🟠 Major** `line 2` — No input sanitization on `id`
If `id` comes from user input, a crafted value could alter the URL path.
> 💡 Fix: Validate `id` matches expected format (e.g., UUID) before using in URL.

### ✅ Best Practices
**🔵 Suggestion** `line 1` — Missing return type annotation
> 💡 Fix: `function getUser(id: string): Promise<User>`

---
## 📊 Review Summary
| Dimension       | Score | Notes                        |
|----------------|-------|------------------------------|
| Code Quality   |  4/5  | Clean, readable               |
| Bug Risk       |  2/5  | Unhandled fetch errors        |
| Security       |  3/5  | Unsanitized path param        |
| Performance    |  N/A  | No perf concerns              |
| Best Practices |  3/5  | Missing types                 |
| **Overall**    | **6/10** | **Fix error handling first** |
```

---

### Scenario 2 — React hook → skill handoff

**User:**
> Review this `useFetchData.ts` hook — I want to refactor it.

**Skill responds:**
> "This looks like a React hook that needs a full refactor. The `react-hooks-auditor`
> skill is better suited — it generates a plan file, typed interfaces, and annotated
> fix output. Want me to switch to that instead, or continue with a quick review here?"

---

### Scenario 3 — Python API handler

**User:**
> Check this for security issues:
> ```python
> def get_user(user_id):
>     query = f"SELECT * FROM users WHERE id = {user_id}"
>     return db.execute(query)
> ```

**Skill output:**
- 🔴 Critical: SQL injection on line 2 — f-string interpolation in raw SQL
- Fix: Use parameterized query: `db.execute("SELECT * FROM users WHERE id = ?", (user_id,))`

---

## Trigger phrases

| What you say | What happens |
|---|---|
| "review this" + code | Full 5-section report |
| "find bugs" | Focuses on Bug Detection section |
| "is this secure?" | Focuses on Security section |
| "check lỗi" | Vietnamese trigger, responds in Vietnamese |
| "có vấn đề gì không" | Same |
| Upload `.ts`, `.py`, `.go`, `.java` | Auto-triggers review |

---

## What it does NOT trigger for

- Doc/spec review → use `doc-reviewer`
- Full hook refactor → use `react-hooks-auditor`
- Unit test generation → use `unit-test-writer`
