# Feature Planner — Examples

## Basic usage

```
Point me to the specs folder and create an implementation plan.
```

```
I have specs in /docs/features/auth — plan this feature out for me.
```

```
Read the docs in ./specs/checkout and tell me how to implement it.
```

---

## Realistic scenarios

### Scenario 1 — New feature from a spec folder

**User:**
> Plan this feature: `./docs/features/user-notifications`

**Skill does:**
1. Scans folder, classifies files by priority
2. Reads `notification-spec.md` (🔴 High) first, then `overview.md` (🟡 Medium)
3. Presents overview plan → asks for confirmation
4. Exports `user-notifications.plan.md` to the folder
5. Asks whether to map affected files
6. Exports `user-notifications.diagram.md`

---

### Scenario 2 — Ambiguous folder with no clear spec

**User:**
> analyze the stuff in `./research/payment-v2` and make a plan

**Skill does:**
- Scans folder, finds only `notes.txt` and `meeting-recording.md`
- Responds: *"I couldn't find a clear spec file. Could you point me to the primary source document?"*

---

### Scenario 3 — User wants to adjust the plan

**User (after seeing overview):**
> Remove the "email digest" part from scope, we're cutting that.

**Skill does:**
- Updates the overview, removes email digest from Key Steps
- Re-confirms, then exports the trimmed plan

---

## Trigger phrases

| What you say | What happens |
|---|---|
| "plan this feature" | Full 4-phase workflow |
| "create an implementation plan" | Full 4-phase workflow |
| "analyze the spec in [folder]" | Phase 0 document scan first |
| "how should I implement this?" | Overview plan + confirmation |
| "read the docs and plan it out" | Full 4-phase workflow |

---

## Output files produced

```
./docs/features/user-notifications/
├── user-notifications.plan.md      ← detailed plan (Phase 2)
└── user-notifications.diagram.md  ← code flow diagram (Phase 4)
```
