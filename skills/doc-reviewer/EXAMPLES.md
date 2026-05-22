# Doc Reviewer — Examples

## Basic usage

```
Review this spec: [paste or upload architecture.md]
```

```
Đánh giá plan này giúp tôi: [paste sprint-plan.md]
```

```
Check this RFC and tell me what's missing.
```

---

## Realistic scenarios

### Scenario 1 — Architecture spec (SPEC mode)

**User:**
> Review this: [pastes system-design.md describing a microservices auth system]

**Skill output:**

```
## 🔍 Doc Review — Technical Spec · system-design.md

### TL;DR
This spec describes a JWT-based auth service deployed on AWS ECS...

### Architecture Overview (ASCII)
[Browser] → [API Gateway] ←→ [Auth Service]
                │
         [PostgreSQL] + [Redis Cache]

### Completeness Scorecard
Tech clarity   █████████░  4.5/5
Scalability    ████████░░  4/5
Security       ████░░░░░░  2/5  ⚠
Observability  ██░░░░░░░░  1/5  ⚠
Risk coverage  ██░░░░░░░░  1/5  ⚠

Overall: 5.5 / 10

### Key Issues
1. ⚠ No rate limiting strategy — auth endpoint is vulnerable to brute force
2. ⚠ Missing observability plan — no logging, alerting, or metrics defined
3. ⚠ No DR/failover for PostgreSQL — single point of failure

### Recommendations
1. [HIGH] Add rate limiting on /auth/login — specify provider (Nginx, API Gateway)
2. [HIGH] Define logging + alerting strategy (CloudWatch / Datadog)
3. [MED]  Document PostgreSQL replica + Redis eviction policy
```

---

### Scenario 2 — Sprint plan (PLAN mode)

**User:**
> audit this plan: [pastes Q3-roadmap.md with milestones and tasks]

**Skill output:** 8-section report including:
- Purpose & Scope review
- Feasibility check (are timelines realistic?)
- Completeness scorecard with ASCII bars
- Recommendations prioritized by impact
- Overall score X/10

---

### Scenario 3 — Ambiguous document

**User:**
> Review this: [pastes a doc that mixes architecture notes with a sprint plan]

**Skill responds:**
> "This document looks like it could be reviewed as a **plan** or a **spec**. Which angle would you prefer?"

---

## Trigger phrases

| What you say | What happens |
|---|---|
| "review this doc" | Detects mode, produces full report |
| "check this spec" | SPEC mode → 5-section report |
| "audit this plan" | PLAN mode → 8-section report |
| "give feedback on this architecture" | SPEC mode |
| "đánh giá doc này" | Detected as Vietnamese, review in English |
| Upload `.md` / `.pdf` / `.docx` | Triggers if doc contains spec or plan content |

---

## What it does NOT trigger for

- Code reviews → use `code-reviewer` skill
- English language learning review → different skill
- Pure code files (`.ts`, `.py`) → politely redirects
