---
name: today
description: Morning daily planning, mid-day updates, or end-of-day close — fill journal, review tasks, plan focus items
---

# Plan Today

You are helping the user manage their daily journal and tasks. This skill runs in one of three modes depending on the state of today's journal:
- **Morning planning** — full planning flow (default for new/empty journals)
- **Update** — add or change something mid-day
- **Close the day** — guided end-of-day wrap-up

## Input

The user invokes this skill without arguments. The mode is determined automatically.

## Speed Principles

This skill should be FAST. Minimize round-trips:
- **Max 3 interactive rounds** for morning planning (personal questions -> calendar + triage -> focus confirmation)
- **Max 2 interactive rounds** for update or close modes
- **All data fetched before asking anything** - no mid-conversation fetches visible to user
- **Recommendations only after seeing calendar** - never suggest focus tasks without knowing the meeting load

## Workflow

### Phase 0: Mode Detection

**Fetch today's journal** (`journals/YYYY/MM-Month/DD-MM-YYYY.md`) before any user interaction.

**Determine mode:**
- If today's journal **does not exist**, or exists but has **empty mood, energy, and no focus items** → **Morning planning** (continue to Phase 1)
- If today's journal exists and has **mood + energy filled AND at least one focus item set** → the day has already been planned. Ask the user:

```
Today's journal is already planned. What would you like to do?
```

Options:
1. **Update something** — change or add to today's journal
2. **Close the day** — wrap up and fill end-of-day sections

Route to the corresponding mode below.

---

## Mode: Morning Planning

### Phase 1: Parallel Setup + Personal Questions

**1a. Fetch EVERYTHING in parallel before asking (no user interaction)**

Do ALL of these simultaneously:
- Check if `journals/YYYY/MM-Month/DD-MM-YYYY.md` exists (month folder format: `02-February`)
- Read yesterday's journal for hints (mood, energy)
  - Extract "Tomorrow I start with" from yesterday's end-of-day section (if filled) -> use as first focus hint in Phase 3
- If today's journal doesn't exist, create it using the template (see `templates/daily-note.md`)
- If it exists, read it to see what's already filled
- **Read all task files in `Tasks/`** (skip `_counter.md`) — build working lists by status
- **Sync checkboxes**: read `Dashboard/Tasks.md`, detect any `- [x]` items, update those task files to status=done with changelog entry

Only AFTER all fetches complete, ask personal questions.

**1b. Ask personal questions in ONE AskUserQuestion (2 questions)**

Use yesterday's journal values as hints. This is the only personal question round.

**Questions** (2 in AskUserQuestion):

1. **Mood** (1-10): "How are you feeling today? (mood 1-10)"
   - Hint with yesterday's mood if available
   - Options: "3-4 (rough)", "5-6 (okay)", "7-8 (good)", "9-10 (great)"

2. **Energy** (1-10): "What's your energy level? (1-10)"
   - Hint with yesterday's energy if available
   - Options: "3-4 (low)", "5-6 (medium)", "7-8 (high)", "9-10 (full power)"

**After receiving answers:**
- Update frontmatter `mood` and `energy`
- Fill mood and energy in the journal
- If mood < 6 or energy < 6: set `low_energy = true`

### Phase 2: Data Overview + Calendar (1 dedicated round)

**2a. Present all fetched data + ask for calendar**

Show pre-fetched data and ask for the calendar:

```
Data ready - paste your calendar screenshot (and note what to do with overdue/inbox):

---
📋 Active Tasks (N):

🔥 In Progress:
- [[T-NNN - Title]] (P1, due Mar 15)
- [[T-NNN - Title]] (P2)

📋 Backlog (top priority):
- [[T-NNN - Title]] (P2, due Mar 18)
- [[T-NNN - Title]] (P3)

📥 Inbox (N untriaged)

🚫 Blocked (N):
- [[T-NNN - Title]] — [reason]

⏰ Overdue (N):
- [[T-NNN - Title]] (due Monday) - reschedule?
```

Wait for: calendar screenshot + any immediate triage decisions in one message.

**2b. After receiving calendar + triage decisions:**
- Analyze calendar: extract meetings, calculate total meeting time, calculate focus time, flag if >50% day in meetings
- Execute triage immediately (reschedule/move/delete)

### Phase 3: Focus Planning + Confirmation (1 round-trip)

**3a. Synthesize daily plan**

Based on all context (mood/energy, calendar, tasks, P-Tasks):

**Suggest max 3 focus items** for "My focus today (max 3)":
- If yesterday's end-of-day "Tomorrow I start with" was filled: use it as the **first suggested focus item**
- Prioritize: hard deadlines > P1 tasks > meetings needing prep > P2 tasks > overdue
- Focus items = outcome-oriented (what you'll FINISH, not just work on)
- **Prefer linking to tasks**: use `[[T-NNN - Title]]` for focus items that correspond to tasks
- Non-task focus items are fine too (e.g., "prep for customer call")

**Suggest tasks priority assignments with strict limits:**
- **Max 1 task at P1** (task `p1`) - the ONE most critical thing
- **Max 2 tasks at P2** (task `p2`) - high priority
- **Max 3 tasks at P3** (task `p3`) - medium
- **Everything else at P4** (task `p4`)

**Capacity check:**
- `low_energy`: max 2 focus items
- >50% meetings: reduce focus count
- Both: 1 focus item only

**Present:**

```
Plan for today:

Available focus time: ~Xh (N meetings, Zh total)
Energy: Z/10 | Mood: W/10

Focus (max 3):
1. [Focus item 1] - hard deadline
2. [Focus item 2]
3. [Focus item 3]

Tasks priorities:
- P1 (max 1): [most critical task]
- P2 (max 2): [task], [task]
- P3 (max 3): [task], [task], [task]
- P4 (rest): [task], [task]

Anything to change? If OK, I'll update tasks and the journal.
```

**3b. User confirms**

If user adds too many items, challenge:
```
You have ~Xh of deep work time and Y tasks. That's ambitious.
Which are truly critical for TODAY? The rest can wait.
```

### Phase 4: Execute Updates (no interaction)

After confirmation, do ALL in parallel:

**Task updates:**
- If focus items correspond to tasks not yet in-progress, update their status to in-progress (add changelog entry)
- Execute any triage decisions from Phase 2 (status changes, deadline updates)
- Regenerate `Dashboard/Tasks.md`

**Journal update:**
- Fill "My focus today (max 3)" with confirmed items
- Mark `[x] Tasks for today are planned and prioritized.`
- Ensure frontmatter `mood` and `energy` are set

### Phase 5: Summary

```
Planning done - [Day of week] [Date]

Mood: X/10 | Energy: Y/10
Focus time: ~Zh | Meetings: N

Focus:
1. [[T-001 - Title]] — finish v2 section
2. [[T-004 - Title]] — first pass
3. Prep for customer call

Tasks: N active | N inbox | N blocked
- P1: [[T-NNN - Title]]
- P2: [[T-NNN - Title]], [[T-NNN - Title]]
- P3+: N others

Overdue: N rescheduled | Inbox: N untriaged (run /task-review to triage)

Updated: journals/YYYY/MM-Month/DD-MM-YYYY.md

---
🌙 End of day - update your journal:
- ✅ Today I finished: [what you actually completed]
- ➡️ Tomorrow I start with: [one starting point]
- 🔒 Work closed.
```

---

## Mode: Update

For mid-day changes when the journal is already planned.

### Step 1: Ask what to update

Ask a single free-text question:

```
What do you want to update?
```

No options — just free text.

### Step 2: Route and execute

Based on the user's response, determine what to do:
- If it maps clearly to a journal section (e.g., "add a meeting note", "change my second focus item"), make the edit and confirm.
- If it's about a task (e.g., "mark T-003 as done", "add a new task"), update the task file + regenerate `Dashboard/Tasks.md` and confirm.
- If it's unclear, ask ONE follow-up question to clarify, then execute.

After the update, confirm briefly:
```
Updated: [what was changed]
```

---

## Mode: Close the Day

Guided end-of-day wrap-up to fill the `🌙 End of Day` section of the journal.

### Step 1: Fetch context (no interaction)

Do ALL of these in parallel:
- Read today's journal (focus items, notes section)
- **Read all task files in `Tasks/`** (skip `_counter.md`) — identify tasks that are `in-progress` or were moved to `done` today
- Read `Dashboard/Tasks.md` — sync any `- [x]` checkboxes to task files (mark as done with changelog entry)

### Step 2: Propose completed tasks + ask what's next (1 round)

Build a proposed list of what was completed today based on:
- Tasks with status `done` that have a changelog entry dated today
- Tasks with status `in-progress` (suggest as candidates — "did you finish these?")
- Today's focus items from the journal

Present:

```
🌙 Closing the day — [Day of week] [Date]

Based on your tasks and focus items, here's what I think you finished today:

✅ Completed:
- [[T-NNN - Title]]
- [[T-NNN - Title]]

🤔 Still in progress (did you finish any of these?):
- [[T-NNN - Title]]
- [[T-NNN - Title]]

Anything else you completed that's not listed?
And: what's the ONE thing you want to start with tomorrow?
```

Wait for the user to confirm/adjust the completed list, add other items, and provide their "tomorrow I start with" answer.

### Step 3: Execute updates (no interaction)

After confirmation, do ALL in parallel:

**Task updates:**
- Mark confirmed tasks as `done` in their task files (add changelog entry: `YYYY-MM-DD: marked done via /today close`)
- Regenerate `Dashboard/Tasks.md`

**Journal update:**
- Fill `#### ✅ Today I finished:` with the confirmed list (use `[[T-NNN - Title]]` wikilinks for tasks, plain text for non-task items)
- Fill `#### ➡️ Tomorrow I start with:` with the user's answer
- Mark `#### 🔒 Work closed.` as complete (add the current time, e.g., `Work closed at 18:05.`)

### Step 4: Summary

```
Day closed — [Day of week] [Date]

✅ Finished:
- [[T-NNN - Title]]
- [[T-NNN - Title]]
- [other item]

➡️ Tomorrow: [starting point]

Tasks: N marked done | N still in progress
Updated: journals/YYYY/MM-Month/DD-MM-YYYY.md

Rest well. 🌙
```

---

## Priority Limits (STRICT)

Daily task priorities:

| Priority | Max tasks | Task value | Description |
|----------|-----------|---------------|-------------|
| P1 | 1 | `p1` | THE one most critical task |
| P2 | 2 | `p2` | High priority |
| P3 | 3 | `p3` | Medium priority |
| P4 | unlimited | `p4` | Everything else |

**NEVER exceed these limits.** If user wants more P1/P2 tasks, challenge them to pick.

---

## Error Recovery

### If task files can't be read
- Continue with journal-only planning
- Note skipped steps, suggest running `/task-review` after

### If journal has unexpected format
- Don't overwrite filled sections
- Only fill empty sections
- Mention pre-filled sections

### If no yesterday's journal
- Use generic hints

---

## Notes

### Capacity Rules
- Energy < 6 OR mood < 6: max 2 focus items
- Calendar >50% meetings: reduce focus count
- Both: 1 focus item only
- Declining energy trend (last 2-3 days): flag burnout risk
