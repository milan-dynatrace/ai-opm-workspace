---
name: process-inbox
description: Review and route unstructured content from Inbox/ into structured notes, then archive originals
---

# Process Inbox

You are a **router** for unstructured files in `Inbox/`. You classify content, delegate to specialized skills when appropriate, handle non-meeting content directly, and extract tasks from everything you process.

## Input

The user invokes this skill without arguments. Optionally, they may name a specific file: `/process-inbox 2026-03-12 - meeting with John.md`

---

## Workflow

### Step 0: Discover inbox files

List all `.md` files in `Inbox/` except `README.md`. If none found:
```
Inbox is empty. Drop any unstructured .md file there and run /process-inbox to route it.
```

If files found, show a numbered list and ask which to process (or "all"):
```
Inbox has N file(s):

1. [filename] — [first line or snippet, max 80 chars]
2. [filename] — [snippet]

Which would you like to process? (number, comma-separated, or "all")
```

---

### Step 1: Read and classify each selected file

For each file, read the full content and determine the **content type**:

| Type | Signals | Route to |
|------|---------|----------|
| **Meeting notes** | Attendees, agenda, discussions, decisions | **Delegate to `/meeting` skill** (full workflow including task extraction) |
| **Decision** | Options, trade-offs, rationale | `Loose Notes/Work/` → decision template |
| **Idea seed** | Speculation, "what if", strategy fragments | Flagged for `/idea` workflow — for now: `Loose Notes/Work/` as loose note |
| **Reference/research** | Pasted articles, links, background reading | `Loose Notes/Work/` → loose note |
| **Task list / action items** | Bullet checkboxes, "need to", "follow up" | `Loose Notes/Work/` → loose note with checkboxes preserved |
| **Braindump / journal** | Stream of consciousness, personal reflection | Today's journal `## Notes` section or `Loose Notes/Work/` |
| **Unclear** | Ambiguous content | Ask the user |

Show classification to the user before acting:
```
Classifying [filename]:
→ Type: [type]
→ Route to: [destination]
→ Summary: [2-sentence summary of the content]

Confirm? (Y / change type / skip)
```

---

### Step 2: Process each confirmed file

**For Meeting notes** — **delegate to the `/meeting` skill entirely**:
- Pass the file path as input to the meeting skill's "specific file" mode
- The meeting skill handles everything: analysis, people cross-referencing, related context search, archiving (to `Archive/Meetings Archive/`), structured note creation, journal linking, and task extraction with user confirmation
- After the meeting skill completes, delete the original from `Inbox/` (the meeting skill already archived it)
- **Do NOT duplicate** any meeting processing logic here — the meeting skill owns it

**For all other types** — process directly as a structured loose note:
- Location: `Loose Notes/Work/YYYY-MM-DD - [Inferred Title].md`
- Template: `templates/loose-note.md`
- Content: Organize the raw content — add a brief summary at the top, preserve original detail below
- Date: Use date from filename if present, otherwise today

**For braindumps/journal fragments**:
- If today's journal exists, append to `## Notes` section as a linked note
- Create the loose note in `Loose Notes/Work/` with full content

**For tasks/action items**:
- Preserve all `- [ ]` checkboxes in the note
- Include a "Source" line linking to the archive path

---

### Step 3: Extract tasks from non-meeting content

**This step applies to all non-meeting content types** (decisions, ideas, references, task lists, braindumps). Meeting notes skip this step — the `/meeting` skill handles their task extraction.

Scan the processed content for potential tasks:
- Explicit action items: `- [ ]` checkboxes, "need to", "should", "follow up", "TODO"
- Commitments: "I'll", "I will", "I need to", "let me"
- Deadlines: anything with a date or timeframe attached to an action

**ONLY extract tasks for the vault owner** — skip tasks owned by others.

Check existing task files in `Tasks/` to avoid duplicates (match by title similarity).

**Present proposed tasks and wait for confirmation before creating:**
```
📋 Potential tasks found in [filename]:
1. [Task description] — Priority: P3
2. [Task description] — Priority: P3

⏭️ Already exists (will skip):
- [[T-NNN - Existing task]]

Create these tasks? (Y/N or select which ones, e.g. "1" or "1,2")
```

After confirmation:
- Create task files in `Tasks/` using the `/task` skill's create workflow (read counter, create files, increment counter)
- Source field: wikilink to the processed note `[[YYYY-MM-DD - Title]]`
- Status: inbox
- Regenerate `Dashboard/Tasks.md`
- Report created tasks: `✅ Created [[T-NNN - Title]]`

If no potential tasks found, skip silently — don't show an empty tasks section.

---

### Step 4: Archive the original (non-meeting files only)

**Meeting files**: already archived by the `/meeting` skill in Step 2 — skip.

**All other files**: after processing:
- Move original from `Inbox/[filename]` to `Archive/Notes Archive/[filename]`
- If a file with the same name exists in archive, append `-2`, `-3`, etc.

---

### Step 5: Link in today's journal (non-meeting files only)

**Meeting files**: already linked by the `/meeting` skill — skip.

**All other files**: add a link in today's journal (`journals/YYYY/MM-Month/YYYY-MM-DD.md`) under `## Notes`:
```
- [[YYYY-MM-DD - Title]] — processed from Inbox ([original filename])
```
If today's journal doesn't exist, skip this step.

---

### Step 6: Summary

```
✅ Processed N file(s) from Inbox

[For each file:]
- [filename] → [[YYYY-MM-DD - Title]] ([type])
  [If tasks were created:] Tasks: [[T-NNN]], [[T-NNN]]

📁 Originals archived

📝 Linked in today's journal: [[YYYY-MM-DD]]

Inbox remaining: N files
```

---

## Notes

- **Skip `README.md`** — never process or archive it
- If the file has a date in its name, use that as the note date — don't default to today unless no date is present
- If content type is unclear, always ask rather than guess
- For meeting notes found in Inbox, apply the full `/meeting` workflow — same quality, same output
- Preserve all original content — never discard anything from the raw file
- If a file is already structured (has frontmatter, has headers matching templates), warn the user before processing

---

## Error Recovery

### If no journal exists for today
- Skip journal linking, note it in the summary

### If archive folder doesn't exist
- Create `Archive/Notes Archive/` if needed before moving

### If file cannot be classified
- Leave it in Inbox
- Show in summary as "skipped — needs manual classification"
