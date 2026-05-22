# Frontend Developer — Examples

## Basic usage

```
Build a responsive user profile card component in React 18 + Next.js 14.
Design is in Figma: [link]. Target breakpoints: 320/768/1440px.
```

```
Create a multi-step form with validation using React Hook Form and Zod.
Framework: React 18. Spec: users fill in name, email, address across 3 steps.
```

---

## Realistic scenarios

### Scenario 1 — Full component from Figma

**User:**
> Build a `<ProductCard>` component. Framework: React 18 + Tailwind CSS.
> Figma: [link]. Breakpoints: 320 / 768 / 1440px.

**Skill does:**
1. Confirms all required inputs are present
2. Analyzes component breakdown (card, image, price badge, CTA button)
3. Defines TypeScript interfaces for props
4. Writes full plan + implementation to `TODO_frontend-developer.md`
5. Confirms in chat: ✅ Written to `TODO_frontend-developer.md`

---

### Scenario 2 — Missing framework info

**User:**
> Build a dropdown menu component.

**Skill responds:**
> Which framework: React, Vue, or Angular?

---

### Scenario 3 — State management decision

**User:**
> Build a shopping cart feature. React 18 + Next.js 14. Cart must persist across pages.

**Skill does:**
- Identifies global state need → recommends Zustand
- Plans Server Component for initial data, Client Component for interactivity
- Documents decision in `TODO_frontend-developer.md` under Component Architecture

---

## Trigger phrases

| What you say | What happens |
|---|---|
| "build a [component] in React" | Full workflow, asks for missing info |
| "create a form with validation" | Asks for framework if not specified |
| "implement [feature] from this Figma" | Confirms breakpoints, writes plan |

---

## Output

All implementation goes to `TODO_frontend-developer.md` in the working directory.

```
TODO_frontend-developer.md
├── Context (framework, design source, perf budget)
├── Implementation Plan (FE-PLAN-x.x)
├── Implementation Items (FE-ITEM-x.x with props, state, a11y, perf notes)
├── Proposed Code Changes (patch-style diffs)
└── Commands (local dev + CI)
```

Skill confirms in chat with a single line — never dumps code inline.
