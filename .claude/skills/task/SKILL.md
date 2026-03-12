---
name: task
description: "Create, update, or query tasks — low-friction CRUD for the task tracking system"
argument-hint: "[action] [details] — e.g. 'add: draft Q2 roadmap', 'T-003 blocked waiting on legal', 'show T-001', 'list in-progress'"
---

# Task

You are managing tasks in the vault's task tracking system. Tasks live as individual files in `Tasks/` and a generated dashboard in `Dashboard/Tasks.md`.

## Input

The user provides a natural language instruction. Parse the intent:

| Intent | Examples |
|--------|----------|
| **Create** | "add task: draft Q2 roadmap", "new task write API docs due March 20", "task: review design spec" |
| **Update status** | "T-003 blocked waiting on legal", "move T-005 to in-progress", "T-002 cancelled — deprioritized" |
| **Update fields** | "set T-005 deadline to March 20", "T-003 priority P1", "T-004 owner Sarah" |
| **Add comment** | "T-003 comment: got partial feedback from eng", "note on T-005: waiting for design review" |
| **Query single** | "show T-001", "task T-003 details", "what's T-005?" |
| **Query list** | "list in-progress", "show blocked tasks", "what's in my inbox?", "all P1 tasks" |
| **Quick batch create** | "add tasks: 1) write docs, 2) review spec, 3) schedule sync" |

If the intent is ambiguous, infer the most likely action and proceed. Only ask if genuinely unclear.

---

## Task System Reference

### Storage
- **Task files**: `Tasks/T-NNN - Title.md` (one file per task)
- **Counter**: `Tasks/_counter.md` (frontmatter `next_id` field)
- **Dashboard**: `Dashboard/Tasks.md` (generated, not manually edited)

### Statuses
```
inbox ↔ backlog → in-progress → done
                ↘ blocked (needs reason)
          any state → inbox (deprioritized/delayed)
          any state → cancelled (needs reason)
```

### Fields
| Field | Required | Default | Notes |
|-------|----------|---------|-------|
| id | auto | T-NNN | Auto-incrementing, zero-padded to 3 digits |
| status | auto | inbox | |
| priority | no | _(empty)_ | P1-P5, set during review or explicitly |
| owner | no | Milan | Only change if delegated |
| deadline | no | _(empty)_ | YYYY-MM-DD format |
| source | no | _(empty)_ | Wikilink to originating note |
| type | no | task | `task` or `goal` — never forced, only set if user specifies |
| created | auto | today | YYYY-MM-DD |

### Template
See `templates/task-note.md` for the file structure.

---

## Workflows

### Create Task

1. Read `Tasks/_counter.md` → get `next_id`
2. Format ID: `T-` + zero-padded to 3 digits (e.g., `T-001`, `T-012`)
3. Create file `Tasks/T-NNN - [Title].md` using template:
   - Fill frontmatter: id, status=inbox, owner=Milan, created=today
   - Fill any fields the user provided (deadline, priority, source, type)
   - Title as `# heading`
   - Description if provided (otherwise leave empty)
   - Changelog: `- YYYY-MM-DD — Created` (add source info if applicable)
4. Increment `next_id` in `Tasks/_counter.md`
5. Regenerate `Dashboard/Tasks.md`
6. Report: `✅ Created [[T-NNN - Title]] (inbox)`

**Minimal creation**: Only a name is required. Everything else optional.

**Batch creation**: For multiple tasks, create all files, increment counter once at the end, regenerate dashboard once.

### Update Status

1. Read the task file
2. Validate the transition:
   - `blocked` and `cancelled` require a reason (ask if not provided)
   - Moving to `inbox` from any state is always valid (deprioritization)
   - `done` can come from any active state
3. Update frontmatter `status`
4. Append to changelog: `- YYYY-MM-DD — Status: [old] → [new]` (include reason for blocked/cancelled)
5. Regenerate `Dashboard/Tasks.md`
6. Report: `✅ T-NNN: [old] → [new]`

### Update Fields

1. Read the task file
2. Update the specified frontmatter field(s)
3. Append to changelog: `- YYYY-MM-DD — [Field]: [old] → [new]`
4. Regenerate dashboard if priority changed (affects ordering)
5. Report: `✅ T-NNN: [field] updated`

### Add Comment

1. Read the task file
2. Append to changelog: `- YYYY-MM-DD — [Comment text]`
3. No dashboard regeneration needed
4. Report: `✅ Comment added to T-NNN`

### Query Single

1. Read the task file
2. Display: title, status, priority, owner, deadline, source, type, last 3 changelog entries
3. If user wants full changelog, show all entries

### Query List

1. Read all task files in `Tasks/` (skip `_counter.md`)
2. Filter by the requested criteria (status, priority, owner, etc.)
3. Display as a compact list:
   ```
   📋 [Status] tasks (N):
   - [[T-001 - Title]] (P2, due Mar 15) — last: [latest changelog entry]
   - [[T-003 - Title]] (P1) — last: [latest changelog entry]
   ```

---

## Dashboard Regeneration

When regenerating `Dashboard/Tasks.md`:

1. Read ALL task files in `Tasks/` (skip `_counter.md`)
2. Group by status: in-progress, backlog, inbox, blocked
3. Within each group, sort by: priority (P1 first, empty last), then deadline (soonest first), then created date
4. **Skip done and cancelled tasks** — they're not shown on the dashboard
5. Write the dashboard with this structure:

```markdown
---
tags: Dashboard
updated: YYYY-MM-DD
---
# Tasks

## 🔥 In Progress
- [ ] [[T-NNN - Title]] (P1, due Mar 15) — @Owner
- [ ] [[T-NNN - Title]] (P2) — @Owner — _last: [latest changelog]_

## 📋 Backlog
- [ ] [[T-NNN - Title]] (P3, due Mar 20) — @Owner
- [ ] [[T-NNN - Title]] — @Owner

## 📥 Inbox
- [ ] [[T-NNN - Title]] — @Owner
- [ ] [[T-NNN - Title]] — @Owner

## 🚫 Blocked
- [ ] [[T-NNN - Title]] (P2) — @Owner — _blocked: [reason]_

---
*Last updated: YYYY-MM-DD HH:MM*
*Done/cancelled tasks not shown. Ask me for history.*
```

**Formatting rules for dashboard lines:**
- Always starts with `- [ ] [[T-NNN - Title]]`
- Priority in parentheses if set: `(P2)`
- Deadline appended if set: `(P2, due Mar 15)`
- Owner always shown: `— @Owner`
- For blocked tasks: append `— _blocked: [reason from changelog]_`
- For in-progress/backlog with changelog: optionally append `— _last: [brief latest entry]_`
- Empty sections show `_No tasks [status]._`

### Checkbox Sync

When any task-aware skill runs, check `Dashboard/Tasks.md` for checked boxes (`- [x]`):
- For each checked item, extract the task ID
- Update that task file: status → done, add changelog entry
- Regenerate the dashboard (which removes done tasks)

---

## Notes

- **Speed matters** — task creation should feel instant. Don't ask unnecessary questions.
- If user says "add task: X" — just create it. Don't ask for priority, deadline, etc. unless offered.
- When creating from another skill (meeting, decision, etc.), the `source` field should contain a wikilink to the originating note.
- Task IDs are permanent — never reuse or renumber.
- The dashboard is always regenerated, never manually patched.
- If a task title is very long, truncate sensibly for the filename but keep full title in the `# heading`.
- Maximum filename-safe title length: ~60 characters. Truncate with `...` if needed.
