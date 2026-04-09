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
- **Active task files**: `Tasks/T-NNN - Title.md` (one file per task — only active tasks: inbox, backlog, in-progress, blocked)
- **Archived tasks**: `Tasks/Archive/YYYY/MM-Month/T-NNN - Title.md` (done/cancelled tasks, filed by completion/cancellation date)
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
| owner | no | [YOUR NAME] | Only change if delegated |
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
   - Fill frontmatter: id, status=inbox, owner=[YOUR NAME], created=today
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
5. **If new status is `done` or `cancelled`**: archive the task file:
   - Determine archive path: `Tasks/Archive/YYYY/MM-Month/` based on today's date (the completion/cancellation date)
   - Create the archive folder if it doesn't exist
   - Move the task file from `Tasks/` to the archive folder
6. **If new status is `done`**: update today's daily journal (see **Daily Journal Updates** below):
   - Strike through matching focus item
   - Append task to "Today I finished" section
7. Regenerate `Dashboard/Tasks.md`
8. Report: `✅ T-NNN: [old] → [new]` (add `(archived)` if moved)

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

1. Read the task file (check `Tasks/` first, then `Tasks/Archive/` if not found)
2. Display: title, status, priority, owner, deadline, source, type, last 3 changelog entries
3. If user wants full changelog, show all entries

### Query List

1. Read active task files in `Tasks/` (top-level only, skip `_counter.md` and `Archive/`)
2. Filter by the requested criteria (status, priority, owner, etc.)
3. If user explicitly asks for done/cancelled/archived/history tasks, also read `Tasks/Archive/` recursively
4. Display as a compact list:
   ```
   📋 [Status] tasks (N):
   - [[T-001 - Title]] (P2, due Mar 15) — last: [latest changelog entry]
   - [[T-003 - Title]] (P1) — last: [latest changelog entry]
   ```

---

## Dashboard Regeneration

When regenerating `Dashboard/Tasks.md`:

1. Read active task files in `Tasks/` (top-level only, skip `_counter.md` and `Archive/`)
2. Group by status: in-progress, backlog, inbox, blocked
3. Within each group, sort by: deadline (earliest first, tasks with no deadline at bottom), then task ID (ascending) for tasks with the same or no deadline
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
- Archive the task file to `Tasks/Archive/YYYY/MM-Month/` based on today's date
- Update today's daily journal for each completed task (see **Daily Journal Updates** below):
  - Strike through matching focus items
  - Append tasks to "Today I finished" section
- Regenerate the dashboard (which removes done tasks)

### Daily Journal Updates

Whenever a task is marked as `done` (via status update, checkbox sync, or day close), update today's daily note (`journals/YYYY/MM-Month/YYYY-MM-DD.md`) with **both** actions below. If today's daily note doesn't exist, skip silently.

#### Focus Item Strikethrough

Check the `#### 🚀 My focus today (max 3):` section for the task ID.

**How to match**: Search focus item lines (numbered list under the heading) for the task ID pattern `T-NNN` (e.g., `T-018`).

**How to strike through**: Wrap the entire focus item text (after the number prefix) in `~~...~~`. Example:

```
Before: 1. [[T-018 - Answer Kay's questions]] — finish & send (due today) 🔥
After:  1. ~~[[T-018 - Answer Kay's questions]] — finish & send (due today) 🔥~~
```

**Rules**:
- Only strike through if the focus item contains the completed task's ID
- If a focus line references multiple tasks (e.g., `[[T-011 - ...]] + [[T-021 - ...]]`), only strike through when ALL referenced tasks are done
- Skip if the line is already struck through

#### Completion Log in "Today I finished"

Append the completed task to the `#### ✅ Today I finished:` section as a bullet: `- [[T-NNN - Title]]`.

**Rules**:
- Append to the existing list — never overwrite items already there
- If the section only has `- ` (empty placeholder), replace it with the first entry
- Skip if a bullet containing the same `T-NNN` ID already exists (avoid duplicates)
- This applies whether or not the task was in the focus list — all completed tasks get logged

---

## Notes

- **Speed matters** — task creation should feel instant. Don't ask unnecessary questions.
- If user says "add task: X" — just create it. Don't ask for priority, deadline, etc. unless offered.
- When creating from another skill (meeting, decision, etc.), the `source` field should contain a wikilink to the originating note.
- Task IDs are permanent — never reuse or renumber.
- The dashboard is always regenerated, never manually patched.
- If a task title is very long, truncate sensibly for the filename but keep full title in the `# heading`.
- Maximum filename-safe title length: ~60 characters. Truncate with `...` if needed.
