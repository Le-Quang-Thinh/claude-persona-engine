# doc-reviewer

Reviews technical documents and project plans, producing a structured Markdown report in chat. Works in two modes: **Plan** (roadmaps, sprints, task lists) and **Spec** (architecture docs, RFCs, system design).

## What it does

**Plan mode** — deep 8-section review:
- Purpose & scope, Strengths, Issues & gaps, Feasibility, Completeness scorecard (ASCII bar chart), Recommendations, Overall score

**Spec mode** — visual summary + quick review:
- TL;DR, Architecture ASCII diagram, Completeness scorecard, Key issues, Recommendations

## Install

```bash
cp -R skills/doc-reviewer /your-project/.claude/skills/
```

No slash command — triggered automatically when you paste or attach a document.

## Usage

Paste or attach a document and ask:
- "review this doc"
- "check this spec"
- "give feedback on this architecture"
- "what's wrong with this plan?"
- "đánh giá doc này"
- "review giúp tôi"

## Example output (Spec mode)

```
## TL;DR
[3-5 sentence summary]

## Architecture Overview
[Browser] → [CDN] → [API Gateway] ←→ [Auth Service]
                          │
                    [Primary DB]

## Completeness Scorecard
Tech clarity   █████████░  4.5/5
Scalability    ████████░░  4/5
Security       ████░░░░░░  2/5  ⚠
Observability  ██░░░░░░░░  1/5  ⚠

Overall: 6.5/10

## Key Issues
1. ⚠ No monitoring strategy defined
2. ⚠ Session token storage unspecified
```

## Does NOT trigger for

- English learning reviews (different skill)
- Code reviews (use `code-reviewer`)
