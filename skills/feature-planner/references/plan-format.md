# Plan Format Reference

Defines the standard format for `[feature-name].plan.md` generated in Phase 2.

---

## Full Template

```markdown
# Plan: [Feature Name]

> **Generated:** [date]
> **Source docs:** [list of spec files read]
> **Status:** Draft

---

## 1. Objective

[Clearly describe what this feature does, why it's needed, and what problem it solves for the user]

---

## 2. Scope

### In scope
- [What will be done]

### Out of scope
- [What will NOT be done in this iteration]

---

## 3. Implementation Steps

### Step 1: [Step name]
**Goal:** [what this step achieves]
**Details:**
- [Sub-task 1]
- [Sub-task 2]
**Output:** [concrete deliverable from this step]

### Step 2: [Step name]
...

---

## 4. Edge Cases & Risks

| Edge Case | How to Handle |
|-----------|---------------|
| [Case 1] | [Handling approach] |
| [Case 2] | [Handling approach] |

---

## 5. Acceptance Criteria

- [ ] [Criterion 1 — must be testable]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

---

## 6. Open Questions

- [ ] [Questions to clarify with team or stakeholders]

---

## 7. Notes

[Additional notes, technical decisions, trade-offs]
```

---

## Writing Guidelines

- **Objective**: Write from the user's perspective, not the system's — *"Users can do X"* rather than *"The system will implement Y"*
- **Implementation Steps**: Detailed enough for another developer to follow without needing code-level explanations
- **Edge Cases**: Focus on what's easy to miss — empty states, error cases, concurrent requests, permission boundaries, etc.
- **Acceptance Criteria**: Every criterion must be **testable** — answerable with Yes or No
- **Out of scope**: Critical for preventing scope creep — explicitly list what will be handled in a future iteration
