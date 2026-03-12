---
name: ideas-review
description: Review all ideas and thoughts, find themes, surface promotable thoughts, update statuses
argument-hint: "[theme to filter by — optional]"
---

# Review Ideas & Thoughts

You are helping the user get a clear picture of their idea landscape — what exists, how ideas cluster, what needs attention, and what's ready to be developed or synthesized.

## Input

Optional: a theme name or keyword to filter the review to a specific area.
No arguments = full review across everything.

---

## Workflow

### Step 1: Read everything (no user interaction yet)

Read in parallel:
- All files in `Ideas/` — full content including frontmatter
- `Thoughts/` — current month and prior month
- Recent journals (last 14 days) for recurring topics and signals
- `Loose Notes/Work/` — recent decisions or notes that might connect to ideas
- `Dashboard/Weekly P-Tasks.md` — current priorities

---

### Step 2: Build the landscape

**2a. Cluster ideas by inferred themes**

Don't just use `themes` frontmatter tags — read the full content of each idea and infer what it's really about.

Group ideas into thematic clusters. For each cluster:
- Give the cluster a name (use existing theme tags if they fit, otherwise infer a label)
- List the ideas in it
- Note if a cluster has no `ready` or `growing` ideas (i.e., all seeds) — flag as "underdeveloped area"

**2b. Status inventory**

List all ideas by status:
- `seed` — just created, not developed
- `growing` — in active development
- `ready` — ready to synthesize
- `archived` — no longer active
- `shipped` — turned into an output

**2c. Staleness check**

Flag ideas where `updated` date is more than 14 days ago and status is `seed` or `growing` — these are candidates to either develop or archive.

**2d. Surface promotable thoughts**

Scan the Thoughts log for entries that:
- Have 2+ annotations (signal of ongoing engagement)
- Appear thematically related to an existing `growing` idea (could enrich it)
- Express a tension or question that no existing idea addresses

List candidates for promotion with a brief explanation of why each one is ready.

**2e. Spot cross-idea tensions or opportunities**

Look for:
- Two ideas that assume conflicting things — flag as a tension to resolve
- Two ideas that would be stronger combined — flag as a merge candidate
- An idea that a recent decision in `Loose Notes/Work/` has already answered — flag as potentially `archived`

---

### Step 3: Present the review

Show a structured summary. Adapt length to what was found — don't pad with empty sections.

```
## Ideas Review — [Date]

### 🗺️ Landscape ([N] ideas, [N] thoughts)

**[Theme cluster 1]** ([N] ideas)
- [[Idea Title]] — [status] — [one-line description]
- [[Idea Title]] — [status] — [one-line description]

**[Theme cluster 2]** ([N] ideas)
- ...

Underdeveloped areas (all seeds, nothing growing): [list or "none"]

---

### 📊 By Status
seed: N | growing: N | ready: N | archived: N | shipped: N

Stale (>14 days, seed/growing):
- [[Idea Title]] — last updated YYYY-MM-DD

---

### 💭 Promotable Thoughts
- [Thought snippet, date] — [why it's ready]
- [Thought snippet, date] — [why it's ready]

---

### ⚡ Flags
- **Tension**: [[Idea A]] vs [[Idea B]] — [what conflicts]
- **Merge candidate**: [[Idea A]] + [[Idea B]] — [why]
- **Possibly answered**: [[Idea]] — already addressed by [[Decision note]]
```

---

### Step 4: Interactive updates

Ask:
```
What would you like to do?

1. Update status of one or more ideas
2. Add or change themes on ideas
3. Promote a thought to an idea
4. Archive a stale idea
5. Nothing — just reviewing

(Enter number(s) or describe what you want)
```

**For status updates:**
- Ask: "Which ideas, and what status?" — accept multiple at once ("1 → growing, 3 → archived")
- For each update: change `status` and `updated` in frontmatter, append to Progress Log: `YYYY-MM-DD: Status → [new status]`

**For theme changes:**
- Suggest themes based on your cluster analysis
- Accept user's own label — don't force your suggestions
- Update `themes` frontmatter array for affected files

**For promotions:**
- Run the `/idea promote` flow for the selected thought

**For archiving:**
- Append to Progress Log: `YYYY-MM-DD: Archived — [reason if given]`
- Update status to `archived`

---

### Step 5: Summary

```
✅ Review complete — [Date]

Updates made:
- [[Idea]] → [new status]
- [[Idea]] themes updated: [new themes]
- [N] thoughts identified as promotable

→ Run /develop [idea] to stress-test a specific idea
→ Run /synthesize [theme] to turn a cluster into an output
```

---

## Notes

- Theme inference should reflect the *actual content* of each idea — read it, don't just scan the title
- Cross-idea tensions are valuable — don't smooth them over, surface them clearly
- If the Ideas folder is empty, focus the review entirely on promotable thoughts from the log
- If filtering by theme (`$ARGUMENTS`), still run the full read step — just narrow the presentation
