# frontend-developer

Senior frontend engineer mode. Plans and implements UI features with a strict priority order: **(1) type safety → (2) accessibility → (3) performance**.

## What it does

Follows a 5-step workflow:
1. **Requirements analysis** — component breakdown, state scope, responsive plan
2. **Component architecture** — TypeScript interfaces, state solution, error boundaries
3. **Implementation** — semantic HTML, mobile-first CSS, ARIA from the start
4. **Performance optimization** — lazy loading, memoization, virtualization
5. **QA & review** — cross-browser, WCAG AA contrast, keyboard nav, Core Web Vitals

All output is written to `TODO_frontend-developer.md` — not inline in chat.

## Install

```bash
cp -R skills/frontend-developer /your-project/.claude/skills/
cp skills/frontend-developer/command.md /your-project/.claude/commands/fe.md
```

## Usage

### Via slash command
```
/fe build a ProgramFilter component
/fe refactor ProgramCard layout for mobile
/fe add accessibility to CourseTable
```

### Via natural language
- "build a search filter component"
- "refactor the header for mobile"
- "implement dark mode toggle with accessibility"

## Required input (Claude will ask if missing)

- Framework & version (e.g., React 18 + Next.js 14)
- Component name or feature description
- Design source (Figma link / written spec / screenshot)
- Target breakpoints (default: 320 / 768 / 1024 / 1440 / 2560px)

## Output file structure (`TODO_frontend-developer.md`)

```
### Context
- Framework & version:
- Design source:
- Performance budget:

### Implementation Plan
- [ ] FE-PLAN-1.1 [Feature Name]

### Implementation Items
- [ ] FE-ITEM-1.1 [Component Name]
  - Props / State / Accessibility / Performance

### Proposed Code Changes
[Patch-style diffs]

### Commands
[Exact dev/CI commands]
```

## QA checklist (sign-off before done)

- TypeScript compiles without errors
- Responsive at 320 / 768 / 1024 / 1440 / 2560px
- All interactive elements keyboard accessible
- WCAG AA color contrast
- Core Web Vitals > 90 (Lighthouse)
- Bundle < 200KB gzipped
