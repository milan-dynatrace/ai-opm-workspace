---
name: synthesize
description: Turn a cluster of ideas into a concrete output — plan, narrative, strategy brief, or decision
argument-hint: "[theme name, idea titles, or 'all ready ideas']"
---

# Synthesize Ideas into an Output

You are helping turn a set of developed ideas into a concrete, usable output — a plan, a narrative, a strategy brief, or a structured decision.

This skill is for when thinking is done and something needs to be made.

## Input

The user provides via `$ARGUMENTS`:
- A theme name: `synthesize platform` — use all ideas tagged with or inferred to belong to that theme
- One or more idea titles: `synthesize agentic observability, explain-ability gap`
- `all ready` — use all ideas with `status: ready`
- Nothing — list available ideas and ask which to include

---

## Workflow

### Step 1: Identify and read source ideas

**If filtering by theme or title**: find matching files in `Ideas/`, read full content
**If `all ready`**: read all files in `Ideas/` where `status: ready`
**If no arguments**: list all ideas by status and ask: "Which ideas do you want to synthesize? (numbers, theme name, or 'all ready')"

Also read in parallel:
- Related decisions in `Loose Notes/Work/` (search by overlapping keywords and themes)
- Related meeting notes in `Meetings/` (last 30 days, search recursively)
- Recent journals (last 14 days) — for urgency signals and what's been on the user's mind
- `Dashboard/Weekly P-Tasks.md` — for strategic priorities this output should align with

---

### Step 2: Ask for output type

Show the source ideas and ask:

```
Synthesizing [N] ideas:
- [[Idea 1]] — [one-line description]
- [[Idea 2]] — [one-line description]

What output do you need?

1. **Plan** — structured list of initiatives, owners, milestones; what to do and in what order
2. **Narrative** — a story that explains the "why" and "where we're going"; good for alignment and communication
3. **Strategy brief** — a 1-2 page doc with context, bets, trade-offs, and success criteria
4. **Decision** — structured options with pros/cons and a recommendation; follows the /decision format

(Enter number or describe what you need)
```

Wait for selection.

---

### Step 3: Synthesize (pre-work before writing)

Before drafting the output, build a synthesis model internally:

1. **Common thread**: What is the single insight or tension that connects all these ideas?
2. **What's converging**: Where do the ideas agree or point in the same direction?
3. **What's in tension**: Where do ideas pull in different directions — and how should that tension be resolved in the output?
4. **What's still missing**: Is there a gap in the thinking that the output will need to flag rather than paper over?
5. **Strategic fit**: How does this connect to current P-Tasks or recent decisions?

---

### Step 4: Draft the output

**Plan format:**
```markdown
# Plan: [Title]

**Created**: YYYY-MM-DD
**Source ideas**: [[Idea 1]], [[Idea 2]], ...

## Why Now
[The signal or urgency that makes this the right time]

## Strategic Bet
[The one core belief this plan is built on]

## Initiatives
1. **[Initiative name]** — [what, why, who, by when]
2. **[Initiative name]** — ...

## Dependencies & Risks
- [Dependency or risk and how to handle it]

## Success Looks Like
- [Outcome 1]
- [Outcome 2]

## Follow-up Tasks
- [ ] [Task]
```

**Narrative format:**
```markdown
# Narrative: [Title]

**Created**: YYYY-MM-DD
**Source ideas**: [[Idea 1]], [[Idea 2]], ...

## The Problem
[What's broken, missing, or changing]

## The Opportunity
[What becomes possible if we act]

## Our Bet
[The belief we're committing to]

## The Path
[How we get there — 3-4 paragraphs, story arc]

## What Changes
[Concrete outcomes — for users, for the team, for the business]
```

**Strategy brief format:**
```markdown
# Strategy Brief: [Title]

**Created**: YYYY-MM-DD
**Source ideas**: [[Idea 1]], [[Idea 2]], ...

## Context
[Why this matters now — 1 paragraph]

## Our Position
[Where we are today]

## Strategic Bets
1. [Bet 1] — [rationale]
2. [Bet 2] — [rationale]

## Trade-offs We're Making
- **Choosing X over Y** — [reason]

## Success Criteria
- [Measurable or observable outcome]

## Open Questions
- [What we still need to resolve]

## Follow-up Tasks
- [ ] [Task]
```

**Decision format:** Follow the structure from `.claude/skills/decision/SKILL.md`.

---

### Step 5: Show the draft and confirm

Show the full draft to the user. Ask:
```
Draft ready. Looks good to save? Or any sections to revise?
(You can say "change [section]" or "add [thing]" — or just confirm)
```

Iterate until confirmed.

---

### Step 6: Save the output

- **Location**: `Loose Notes/Work/YYYY-MM-DD - [Type] - [Title].md`
- Write the confirmed draft

---

### Step 7: Update source idea files

For each source idea used:
- Append to its Progress Log: `YYYY-MM-DD: Synthesized into [[YYYY-MM-DD - Type - Title]]`
- If appropriate, update status to `shipped`

---

### Step 8: Link in today's journal

Add to `journals/YYYY/MM-Month/YYYY-MM-DD.md` under `## Notes`:
```
- 🧩 Synthesized: [[YYYY-MM-DD - Type - Title]] (from N ideas: [[Idea 1]], ...)
```

---

### Step 9: Propose follow-up tasks

Extract `- [ ]` tasks from the output. Show them and ask for confirmation before treating them as official action items.

---

### Step 10: Output

```
✅ Output created: [[YYYY-MM-DD - Type - Title]]
✅ Linked in today's journal

Source ideas updated:
- [[Idea 1]] → shipped
- [[Idea 2]] → shipped

📋 Follow-up tasks:
- [ ] [Task 1] — P[N]
- [ ] [Task 2] — P[N]

Confirm: Track these tasks? (Y/N)
```

---

## Notes

- **Don't synthesize prematurely** — if source ideas are all `seed` status and haven't been developed, warn the user: "These ideas haven't been stress-tested yet. Consider running `/develop` first, or confirm you want to synthesize with early-stage thinking."
- The output should be *opinionated* — a synthesis that papers over tensions with vague language is not useful. Name the tensions and make a call.
- If the ideas don't cohere into a single output, say so and suggest narrowing: "These ideas pull in different directions — you might get a stronger output by synthesizing [subset] separately."
- Never invent evidence or signals not found in the vault.
