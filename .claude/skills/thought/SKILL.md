---
name: thought
description: Capture a fleeting thought or annotate an existing one in the monthly Thoughts log
argument-hint: "[your thought text] OR annotate [date or keyword]"
---

# Capture or Annotate a Thought

You are helping capture a raw thought into the monthly Thoughts log, or annotate an existing entry with new context.

## Input Modes

| Mode | Arguments | When to use |
|------|-----------|-------------|
| **Capture** | Free text (the thought itself) | Recording a new thought right now |
| **Annotate** | `annotate [date or keyword]` | Adding context to an existing thought without changing the original |
| **Quick capture** | Short text, no interaction | When text is a single sentence — skip all questions, just append |

---

## Workflow

### Mode 1: Capture a new thought

**Step 1: Resolve target file**
- Target file: `Thoughts/YYYY-MM.md` (current month)
- If file doesn't exist, create it with this header:
  ```markdown
  # Thoughts — [Month] [Year]

  <!-- Each entry is a dated block. Never edit originals. Annotate with > quote blocks. -->
  ```

**Step 2: Find related context (do before asking anything)**

Read the following in parallel — do NOT ask the user yet:
- Last 30 entries in `Thoughts/YYYY-MM.md` (current and prior month if needed)
- All files in `Ideas/` — read full content, not just titles
- Recent journals (last 7 days) for what's been on the user's mind
- `Loose Notes/Work/` for related decisions or research

**Reason about connections** — don't just keyword-match. Ask yourself:
- Does this thought share a theme, assumption, or tension with something already captured?
- Does it support, contradict, or extend an existing idea?
- Would someone reading this thought and the related note see an obvious link?

**Step 3: Check if it's a single-sentence quick capture**

If `$ARGUMENTS` is 1-2 sentences with no obvious question in it:
- Skip all questions
- Append directly to `Thoughts/YYYY-MM.md`
- Show the connections found and the summary below — that's the full output

Otherwise, ask one optional question:
> "Anything to add — context, who you were talking to, what triggered this? (or press enter to skip)"

**Step 4: Append the entry**

Format:
```markdown
---

### YYYY-MM-DD
[The thought as captured]

[Context sentence if user provided one]
```

Append to the end of `Thoughts/YYYY-MM.md`.

**Step 5: Link in today's journal**

In `journals/YYYY/MM-Month/YYYY-MM-DD.md` under `## Notes`, add:
```
- 💭 Thought logged: "[first 60 chars of thought]..." → [[Thoughts/YYYY-MM]]
```
If today's journal doesn't exist, skip.

**Step 6: Output**

```
💭 Thought logged → Thoughts/YYYY-MM.md

Related:
- [Connection 1] — [why it's related, 1 sentence]
- [Connection 2] — [why it's related]
(or "No strong connections found yet.")

💡 Promote to idea? If this is worth developing, run: /idea "[title or paste the thought"
```

---

### Mode 2: Annotate an existing thought

**Step 1: Find the target entry**

If user gave a date (e.g., `annotate 2026-03-10`):
- Open `Thoughts/YYYY-MM.md` and find the `### 2026-03-10` block

If user gave a keyword (e.g., `annotate observability`):
- Search `Thoughts/` for entries containing that keyword
- If multiple matches, show them numbered and ask which one

**Step 2: Show the original entry**

```
Found:

### 2026-03-10
[original thought text]

Add your annotation:
```

Wait for the annotation text.

**Step 3: Append the annotation**

Append directly below the original entry (and any existing annotations):
```markdown
> **YYYY-MM-DD**: [annotation text]
```

Never alter the original capture line.

**Step 4: Output**

```
✅ Annotation added to 2026-03-10 entry in Thoughts/YYYY-MM.md

[Original thought]
> [New annotation]
```

---

## Notes

- **Never edit original thought text.** Annotations only.
- The `---` separator goes before each `### date` block to visually separate entries.
- Multiple thoughts on the same day each get their own `### date` block with a `---` separator.
- Related content search is about *conceptual* connections, not keyword matches — explain the link in one sentence.
- If no related content is found, say so — don't fabricate connections.
