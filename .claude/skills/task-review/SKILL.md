---
name: task-review
description: "Ad-hoc task review and prioritization — triage inbox, reprioritize, clean up stale tasks"
argument-hint: "[optional focus] — e.g. 'inbox only', 'blocked tasks', 'full review'"
---

# Task Review

You are running an ad-hoc task review and prioritization session. This replaces the old weekly P-Tasks review — it runs whenever the user wants, not on a fixed cadence.

## Input

The user invokes this skill, optionally with a focus area:

| Focus | What it does |
|-------|-------------|
| _(none)_ / `full review` | Full review: sync checkboxes, triage inbox, review all active tasks |
| `inbox` / `inbox only` | Triage inbox tasks only |
| `blocked` | Review blocked tasks — unblock, cancel, or keep |
| `priorities` | Reprioritize active tasks without triaging inbox |
| `cleanup` / `stale` | Find and clean up stale tasks (no updates in 2+ weeks) |

---

## Workflow

### Phase 0: Sync (always runs first, no interaction)

1. Read `Dashboard/Tasks.md` — detect any checked boxes (`- [x]`)
2. For each checked item:
   - Read task file, update status → `done`, add changelog entry: `- YYYY-MM-DD — Status: [old] → done (marked complete in Obsidian)`
   - Archive the task file to `Tasks/Archive/YYYY/MM-Month/` based on today's date
3. Read active task files in `Tasks/` (top-level only, skip `_counter.md` and `Archive/`)
4. Build working lists by status: inbox, backlog, in-progress, blocked
5. Note any tasks that are stale (no changelog entry in 14+ days)

---

### Phase 1: Overview (present, don't ask yet)

Show the full picture in one block:

```
📊 Task Review — [Date]

Synced: N tasks marked done from dashboard checkboxes

🔥 In Progress (N):
- [[T-001 - Title]] (P1, due Mar 15) — last: [latest changelog]
- [[T-003 - Title]] (P2) — last: [latest changelog]

🚫 Blocked (N):
- [[T-005 - Title]] (P2) — blocked since Mar 10: [reason]

📋 Backlog (N):
- [[T-004 - Title]] (P3, due Mar 20)
- [[T-006 - Title]]

📥 Inbox (N):
- [[T-008 - Title]]
- [[T-009 - Title]]

⚠️ Stale (no updates 14+ days):
- [[T-002 - Title]] — last update: Feb 25

Summary: N active | N inbox | N blocked | N stale
```

Then proceed to the relevant phase based on focus (or all phases for full review).

---

### Phase 2: Triage Inbox (if inbox has items)

For each inbox task, ask for a decision. Present them in a batch:

```
📥 Inbox Triage (N tasks):

1. [[T-008 - Title]]
   → backlog / in-progress / cancel / keep in inbox?
   Priority? Deadline?

2. [[T-009 - Title]]
   → backlog / in-progress / cancel / keep in inbox?
   Priority? Deadline?

You can answer like: "1: backlog P3, 2: in-progress P2 due Mar 20"
```

After user responds:
- Update each task file (status, priority, deadline)
- Add changelog entries
- Don't regenerate dashboard yet (wait until end)

---

### Phase 3: Review Blocked Tasks (if any blocked)

For each blocked task:

```
🚫 Blocked Tasks:

1. [[T-005 - Title]] — blocked since Mar 10: [reason]
   → unblock (→ in-progress) / keep blocked / cancel / → inbox?

Answer like: "1: unblock" or "1: keep, added note to follow up Friday"
```

After user responds:
- Update task files
- If comment provided, add to changelog

---

### Phase 4: Review Stale Tasks (if any stale)

```
⚠️ Stale Tasks (no updates 14+ days):

1. [[T-002 - Title]] (backlog, P3) — last update: Feb 25
   → still relevant? keep / → inbox / cancel?
```

---

### Phase 5: Reprioritize (if full review or priorities focus)

Show all active tasks (in-progress + backlog) sorted by current priority:

```
📋 Active Tasks — Priority Check:

P1: [[T-001 - Title]] (in-progress, due Mar 15)
P2: [[T-003 - Title]] (in-progress), [[T-004 - Title]] (backlog, due Mar 20)
P3: [[T-006 - Title]] (backlog)
No priority: [[T-010 - Title]] (backlog)

Current limits: P1 max 1 ✅ | P2 max 2 ✅

Any changes? (e.g. "T-006 to P2, T-004 to P3")
Or "looks good" to keep as-is.
```

**Enforce priority limits:**
- Max 1 P1 task
- Max 2 P2 tasks
- If user exceeds limits, challenge: "You already have N P2 tasks. Which one should drop to P3?"

---

### Phase 6: Finalize (no interaction)

1. Regenerate `Dashboard/Tasks.md`
2. Show summary:

```
✅ Review complete

Changes:
- N tasks triaged from inbox
- N tasks status updated
- N tasks reprioritized
- N tasks marked done (from checkboxes)
- N stale tasks addressed

Dashboard updated: [[Dashboard/Tasks]]
```

---

## Priority Limits (enforced)

| Priority | Max active tasks | Description |
|----------|-----------------|-------------|
| P1 | 1 | THE most critical thing |
| P2 | 2 | High priority |
| P3 | 5 | Medium priority |
| P4 | unlimited | Low priority |
| P5 | unlimited | Optional |

If the user tries to exceed P1 or P2 limits, push back. P3 limit is soft — warn but allow.

---

## Notes

- **This is ad-hoc** — no fixed cadence. Runs when the user feels like it.
- **Always sync checkboxes first** (Phase 0) — this processes any tasks the user marked done in Obsidian.
- **Keep it conversational** — this is a planning session, not a form. The user can change their mind, ask questions, or skip sections.
- If the user says "quick review" or "just inbox" — skip directly to the relevant phase.
- For full reviews, go through all phases but keep each one tight. Don't linger.
- Done and cancelled tasks are never shown unless explicitly asked.
- The dashboard is only regenerated once at the end (Phase 6), not after each change.
