---
name: doc-reviewer
description: >
  Review technical documents (specs, architecture docs, RFCs) and project plans,
  producing a structured in-chat Markdown report. Use this skill WHENEVER the user
  uploads or pastes a document and asks to review, audit, critique, or evaluate it.
  Trigger phrases: "review this doc", "review this plan", "check this spec",
  "audit this plan", "give feedback on this architecture", "what's wrong with this doc",
  "is this spec complete", "đánh giá doc này", "review giúp tôi", or any variation.
  Also trigger when user uploads a .md, .pdf, .docx file containing a technical spec
  or project plan and asks for feedback. DOES NOT trigger for English learning reviews
  (that is study-review skill) or code reviews (different workflow).
---

# Doc Reviewer — Technical Document & Plan Review

## Role

Claude acts as a **senior technical reviewer**. When the user uploads or pastes
a document, Claude analyzes the entire content and produces a structured Markdown
report directly in chat.

## Mode Detection

Claude MUST detect the document type and choose the correct mode:

### How to detect

1. **Plan mode** — Document contains: roadmap, timeline, milestones, task list,
   sprint plan, project phases, deliverables, resource allocation, deadlines.
   Keywords: "plan", "roadmap", "sprint", "milestone", "phase", "timeline",
   "deliverable", "task".

2. **Spec mode** — Document contains: architecture, system design, API design,
   tech stack, database schema, infrastructure, deployment, data flow,
   requirements, constraints, protocols.
   Keywords: "spec", "architecture", "RFC", "design doc", "technical specification",
   "system design", "API", "schema", "infrastructure".

3. **Ambiguous** — If unclear, ask the user:
   "This document looks like it could be reviewed as a **plan** or a **spec**.
   Which angle would you prefer?"

---

## PLAN MODE — Deep 8-Section Review

For project plans, roadmaps, sprint plans, and task-oriented documents.

### Output Format

Produce these 8 sections in order:

#### Section 1 — Document Summary

Write 3-5 sentences:
```
Document: [document name/title]
Type:     [project plan / roadmap / sprint plan / ...]
Scope:    [what this plan covers — 1-2 sentences]
```

#### Section 2 — Purpose & Scope

- Is the goal clearly stated?
- Is scope well-defined? What's in scope vs out of scope?
- Is the target audience clear?

#### Section 3 — Strengths

What's done well? Be specific. Reference actual sections or content from the
document. Minimum 2-3 points.

#### Section 4 — Issues & Gaps

Missing sections, ambiguities, contradictions, unrealistic timelines.
Use this format:
```
- ❌ [issue title] — [1-2 sentence explanation]
```

#### Section 5 — Feasibility Check

- Are timelines realistic?
- Are resources/dependencies identified?
- Are there bottlenecks or single points of failure?
- Are assumptions stated?

#### Section 6 — Completeness Checklist

Rate each dimension using ASCII bar chart:

```
Goals & objectives    ████████░░  4/5
Timeline & milestones ██████░░░░  3/5
Resource allocation   ████░░░░░░  2/5
Risk management       ██░░░░░░░░  1/5
Success metrics       ██████████  5/5
```

Dimensions to rate:
- Goals & objectives
- Timeline & milestones
- Resource allocation
- Risk management
- Success metrics / KPIs
- Dependencies & assumptions

#### Section 7 — Recommendations

Actionable improvements, prioritized by impact:
```
1. [HIGH] [recommendation] — [why it matters]
2. [MED]  [recommendation] — [why it matters]
3. [LOW]  [recommendation] — [why it matters]
```

#### Section 8 — Overall Score

```
Overall: X / 10 — [one-line verdict]
```

---

## SPEC MODE — Visual Summary + Quick Review

For technical specs, architecture docs, RFCs, and system design documents.
Focus on making the document EASY TO UNDERSTAND with simple text diagrams.

### Output Format

Produce these 5 sections in order:

#### Section 1 — TL;DR

3-5 sentences summarizing what this system/spec does, the core tech decisions,
and the target scale. Write for someone who has 30 seconds.

#### Section 2 — Architecture Overview (ASCII Diagram)

Draw a simple ASCII/text diagram showing the main components and how they connect.

Rules for the diagram:
- Use box-drawing characters: `[Component Name]`
- Use arrows: `→`, `←`, `↔`, `│`, `▼`, `▲`
- Keep it under 20 lines
- Group by layer if applicable (Client / Edge / API / Data)
- Add short annotations after `──` for context

Example:
```
[Browser / Client]
       │
       ▼
[CDN Layer]  ──── caching, TLS, DDoS
       │
       ▼
[API Gateway]  ←──→  [Auth Service]
       │
       ├─────────────────┐
       ▼                 ▼
[Primary DB]      [Object Storage]

Deployment: [platform]
Scale:      [target]
```

#### Section 3 — Completeness Scorecard

ASCII bar chart with key dimensions:

```
Scalability    ████████░░  4/5
Security       ████░░░░░░  2/5  ⚠
Tech clarity   █████████░  4.5/5
Observability  ██░░░░░░░░  1/5  ⚠
Risk coverage  ██░░░░░░░░  1/5  ⚠

Overall: X / 10
```

Dimensions to rate (select what's relevant):
- Tech clarity (stack, protocols, data flow)
- Scalability (horizontal, caching, CDN)
- Security (auth, encryption, secrets, rate limiting)
- Observability (logging, monitoring, alerting)
- Risk coverage (failure modes, DR, fallbacks)
- Data design (schema, migrations, consistency)
- API design (versioning, contracts, docs)

Mark dimensions scoring ≤ 2 with ⚠.

#### Section 4 — Key Issues

Top 3-5 issues, sorted by severity:
```
1. ⚠ [issue title] — [1-2 sentence explanation]
2. ⚠ [issue title] — [1-2 sentence explanation]
3. ⚠ [issue title] — [1-2 sentence explanation]
```

#### Section 5 — Recommendations

Same format as Plan mode section 7:
```
1. [HIGH] [recommendation] — [why]
2. [MED]  [recommendation] — [why]
3. [LOW]  [recommendation] — [why]
```

---

## General Rules (Both Modes)

- **Language**: English. If the user writes in Vietnamese, still produce the
  review in English but add Vietnamese notes in parentheses for complex terms
  if helpful.
- **Be direct**: Don't soften genuine problems. If something is missing, say
  it clearly.
- **Be specific**: Reference actual content from the document. Don't use vague
  phrases like "could be improved".
- **N/A sections**: If a section doesn't apply, write "N/A — [brief reason]".
  Don't skip silently.
- **Short documents**: If the document is very brief (under 200 words), note
  this and still produce the full review — brevity itself is usually a problem.
- **Multiple documents**: If the user uploads multiple files, review each
  separately with clear headers.

## Edge Cases

- **Empty or unreadable file**: Tell the user, ask them to re-upload or paste
  the content.
- **Code file instead of doc**: Politely redirect — "This looks like source code
  rather than a spec/plan. Would you like a code review instead?"
- **Mixed doc**: If the document contains both plan and spec elements, use Spec
  mode for the architecture sections and Plan mode for the timeline/milestone
  sections. Clearly label which mode applies to which part.

## ASCII Chart Reference

For the completeness scorecard, use these block characters:

```
█ = filled (score)
░ = empty (remaining)

Scale: 10 blocks total = 5 points
  1/5  = ██░░░░░░░░
  2/5  = ████░░░░░░
  3/5  = ██████░░░░
  4/5  = ████████░░
  5/5  = ██████████
```

Half points (e.g., 4.5/5) use: `█████████░`
