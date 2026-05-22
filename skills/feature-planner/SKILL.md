---
name: feature-planner
description: >
  Reads spec documents in a folder → analyzes → generates a detailed implementation plan → lists affected files → exports a code flow diagram.
  Use this skill WHENEVER the user wants to: plan a new feature, analyze a spec or requirement, create an implementation plan,
  review feature documentation, or asks "how should I implement this feature?".
  Trigger when the user says: "create a plan", "plan this feature", "analyze the spec", "how do I implement this",
  "read the docs and plan it out", "create an implementation plan", or points to a folder containing specs or requirements.
  Do NOT trigger for pure code refactoring tasks (use react-hooks-auditor) or standalone code reviews.
---

# Feature Planner Skill

Analyzes spec documents → generates an implementation plan → produces a code flow diagram following a structured, phased workflow.

> **💡 Recommended setup before running:**
> In Claude Code, enable **Opus Plan Mode** for best results:
> ```
> /model opusplan
> Shift+Tab → enable Plan Mode
> ```
> Opus handles analysis and planning; Sonnet automatically takes over during implementation.

---

## Phase 0 — Document Discovery

Scan the entire specified folder:

1. List all files (`.md`, `.txt`, `.json`, `.yaml`, any format)
2. Classify files by read priority:
   - **🔴 High** — filename contains: `spec`, `requirement`, `prd`, `design`, `feature`, `brief`
   - **🟡 Medium** — filename contains: `readme`, `overview`, `context`, `note`
   - **⚪ Low** — config files, assets, sample code
3. Read **high-priority** files in depth first, then skim the rest
4. Summarize each file briefly: *"File X → covers Y"*

**If no clear spec is found** → notify the user and ask:
> *"I couldn't find a clear spec file. Could you point me to the primary source document?"*

---

## Phase 1 — Overview Plan + Confirmation

After reading all documents, produce a **high-level overview** plan and present it in chat:

```markdown
## Feature: [feature name]

### Objective
[1-2 sentences describing what this feature does and what problem it solves]

### Key Steps
1. [High-level step 1]
2. [High-level step 2]
3. ...

### Assumptions
- [Assumption 1]
- [Assumption 2]

### Open Questions
- [Questions that need clarification]
```

→ After presenting, **ask for confirmation**:
> *"Here's the overview plan. Would you like to adjust anything, or should I dive into the details?"*

Wait for user confirmation before moving to Phase 2.

---

## Phase 2 — Detailed Plan + File Export

Once confirmed, expand into a detailed plan and **automatically export the file** — no further prompting needed.

**Filename:** `[feature-name].plan.md`
**Location:** Save directly inside the working folder

See full format reference at: `references/plan-format.md`

---

## Phase 3 — File Impact Analysis

After exporting the plan, ask the user:

> *"Would you like me to map out which files will be affected when implementing this feature?"*

If **yes**:
1. List files grouped into 3 categories:
   - 🟢 **Create** — new files to be created
   - 🟡 **Modify** — existing files that need changes
   - 🔴 **Delete / Deprecate** — files that are no longer needed

2. Follow up with:
   > *"Are there any files you'd like to remove from or add to this list to better scope the work?"*

3. User responds → update the list → final confirmation

---

## Phase 4 — Diagram + File Export

Once the file list is confirmed, generate a code flow diagram and **automatically export the file**.

**Filename:** `[feature-name].diagram.md`
**Location:** Save in the same folder as the plan

See diagram guidelines at: `references/diagram-guide.md`

---

## Behavior Rules

| Rule | Description |
|------|-------------|
| Phase 0 | Always summarize files read before generating a plan |
| Phase 1 | **Must ask for confirmation** before going into detail |
| Phase 2 | **Auto-export the file** — do not ask again |
| Phase 3 | **Ask first**, then map affected files |
| Phase 4 | **Auto-export the file** once file list is confirmed |
| Filenames | Use kebab-case: `my-feature.plan.md`, `my-feature.diagram.md` |
| Language | Respond in the language the user is using |
