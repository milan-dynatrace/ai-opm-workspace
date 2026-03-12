---
name: process-inbox
description: Review and route unstructured content from Inbox/ into structured notes, then archive originals
---

# Process Inbox

You are helping process unstructured files from `Inbox/` — routing each to the right place, creating structured notes, and archiving the originals.

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
| **Meeting notes** | Attendees, agenda, discussions, decisions | `Meetings/` → process with meeting workflow |
| **Decision** | Options, trade-offs, rationale | `Loose Notes/Work/` → decision template |
| **Idea seed** | Speculation, "what if", strategy fragments | Flagged for `/idea` workflow (future skill) — for now: `Loose Notes/Work/` as loose note |
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

**For Meeting notes** — run the same logic as the `/meeting` skill:
- Extract title, date, attendees, decisions, action items
- Create structured note in `Meetings/YYYY-MM-DD - [Title].md` using `templates/meeting-note.md`
- Link in the meeting's journal date

**For all other types** — create a structured loose note:
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

### Step 3: Archive the original

After processing each file:
- Move original from `Inbox/[filename]` to `Archive/Notes Archive/[filename]`
- If a file with the same name exists in archive, append `-2`, `-3`, etc.

---

### Step 4: Link in today's journal

For each processed file, add a link in today's journal (`journals/YYYY/MM-Month/DD-MM-YYYY.md`) under `## Notes`:
```
- [[YYYY-MM-DD - Title]] — processed from Inbox ([original filename])
```
If today's journal doesn't exist, skip this step.

---

### Step 5: Summary

```
✅ Processed N file(s) from Inbox

[For each file:]
- [filename] → [[YYYY-MM-DD - Title]] ([type])

📁 Originals archived to Archive/Notes Archive/

📝 Linked in today's journal: [[DD-MM-YYYY]]

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
