---
description: Activate React Hooks Auditor — audit and refactor custom React hooks using the Vercel 15-criteria checklist.
---

Load and apply @.claude/skills/react-hooks-auditor/SKILL.md before responding.

Rules:
1. Detect language (TS or JS) from the provided file
2. If JS → pause and present options A / B / C before proceeding
3. Run all 4 phases in order: Plan → Types → Risk Audit → Refactor
4. Auto-save plan to `.claude/plans/refactor-[filename].md` (no confirmation needed)
5. Write refactored output to the plan file, not inline in chat

Usage:
- /audit useProgram.ts
- /audit useAuth.ts
- /audit src/hooks/useCourse.ts
