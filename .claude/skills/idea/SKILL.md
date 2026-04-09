---
name: idea
description: Capture a new idea or promote a thought from the Thoughts log into a tracked idea file
argument-hint: "[idea title or description] OR promote [date or keyword]"
---

# Capture or Promote an Idea

You are helping create a structured idea file in `Ideas/`, either from a fresh description or by promoting an existing thought from the Thoughts log.

## Input Modes

| Mode | Arguments | When to use |
|------|-----------|-------------|
| **Fresh capture** | Title or description of the idea | Idea is already formed enough to name |
| **Promote** | `promote [date or keyword]` | Graduating a thought from the Thoughts log |
| **No arguments** | (none) | Ask the user what the idea is |

---

## Workflow

### Step 1: Get the idea content

**If promoting a thought:**
- Search `Thoughts/` for the entry matching the date or keyword
- If multiple matches, show them and ask which one
- Use the full thought text (original + all annotations) as the source content
- Extract a working title from the thought if the user hasn't provided one

**If fresh capture or no arguments:**
- Use `$ARGUMENTS` as the starting content
- If no arguments: ask "What's the idea? (one sentence to a few lines is enough)"

---

### Step 2: Search for related context (before asking anything else)

Read the following in parallel:
- All files in `Ideas/` — full content
- Current and prior month in `Thoughts/` — read the full log
- Recent journals (last 14 days) — what has been on the user's mind
- `Meetings/` — last 5 meeting notes (search recursively) for relevant discussions
- `Loose Notes/Work/` — related decisions or notes

**Reason about connections** — think like the user, not a search engine:
- What existing ideas share the same underlying assumption or tension?
- What prior decisions constrain or enable this idea?
- What signals from meetings or journals give this idea context or urgency?
- Does this idea resolve a problem that's been surfacing repeatedly in the journals?

Be explicit about *why* each connection is relevant — one sentence per connection.

---

### Step 3: Ask for optional context (one question, light touch)

Show the idea back to the user and the connections found, then ask:

```
Here's what I found related to this idea:

- [Connection 1] — [why]
- [Connection 2] — [why]

Quick questions (skip any you don't want to answer):
1. Does any of these connections need to be in the idea file?
2. Is there a theme or area this belongs to? (e.g., platform, agentic, customer-experience — or leave blank, I'll suggest later)
3. What's the most obvious next step, if any?
```

Wait for response. All fields optional — if user skips, leave them blank or use what you inferred.

---

### Step 4: Create the idea file

- **Location**: `Ideas/IDEA-XXX - [Title].md`
- **Template**: `templates/idea-note.md`
- **ID**: Read `Ideas/_counter.md` to get the next ID. Format as `IDEA-XXX` (zero-padded to 3 digits, e.g., `IDEA-001`). After creating the file, increment `next_id` in `Ideas/_counter.md`.
- **Date**: today

Fill the sections:
- **The Idea**: the core concept, distilled — if promoting, use the thought text + annotations as source
- **Why This Matters**: infer from the thought/context, or leave with placeholder if not clear
- **Evidence & Signals**: populate from connections found (meeting references, journal signals, etc.)
- **Related**: wikilinks to connected ideas, thoughts, meetings, decisions
- **Progress Log**: single entry `YYYY-MM-DD: Created` (+ `Promoted from Thoughts/YYYY-MM.md — YYYY-MM-DD entry` if applicable)
- **Next Step**: from user's answer, or leave as placeholder
- **themes**: from user's answer, or empty array `[]`
- **Source**: thought log reference if promoting, otherwise "Direct capture"

---

### Step 5: If promoting — mark the thought as promoted

In `Thoughts/YYYY-MM.md`, append an annotation to the source entry:
```markdown
> **YYYY-MM-DD**: Promoted to idea → [[Ideas/IDEA-XXX - Title]]
```

---

### Step 6: Link in today's journal

In `journals/YYYY/MM-Month/YYYY-MM-DD.md` under `## Notes`:
```
- 💡 New idea: [[IDEA-XXX - Title]] ([status: seed])
```

---

### Step 7: Output

```
💡 Idea created: [[IDEA-XXX - Title]]
Status: seed

Related context linked:
- [Connection 1] — [why]
- [Connection 2] — [why]

Next step: [next step or "none set"]
Themes: [themes or "none — add with /ideas-review or /develop"]

→ Run /develop to stress-test this idea
→ Run /ideas-review to see how it fits with other ideas
```

---

## Notes

- **Never force themes** — if the user doesn't provide them, leave `themes: []`. They emerge through `/ideas-review`.
- The idea file date is always today, even if the source thought is older.
- If the source thought has annotations, treat the annotations as part of the idea's origin context, not separate.
- Related content search is conceptual — explain the link, don't just list file names.
- If two existing ideas are very similar to this new one, flag it explicitly: "This idea overlaps strongly with [[Existing Idea]] — consider developing that one instead or merging."
