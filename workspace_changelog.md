# Workspace Changelog

All structural changes to this workspace are logged here — new folders, new skills, updated conventions, renamed files, and changes to `CLAUDE.md` or `copilot-instructions.md`.

---

## 2026-03-14 — Remove self-care and yesterday evening from daily flow

### Updated

- `.claude/skills/today/SKILL.md` — removed self-care and yesterday evening questions from Phase 1b. Reduced from 3-4 questions to 2 (mood + energy only).
- `templates/daily-note.md` — removed "Yesterday" section (after work, energy/drain) and "What did I do for myself before work?" question. Template now starts with Today section containing mood and energy only.

---

## 2026-03-14 — `/today` skill: multi-mode support (morning, update, close)

### Updated

- `.claude/skills/today/SKILL.md`
  - Added **Phase 0: Mode Detection** — checks if today's journal is already planned (mood + energy + focus items filled). If so, asks whether to run Update or Close the Day mode instead of morning planning.
  - Added **Mode: Update** — free-text question for mid-day changes. Routes to the right journal section or task file based on the response. One follow-up if unclear.
  - Added **Mode: Close the Day** — guided end-of-day wrap-up. Reads task files and dashboard to propose completed tasks (done today + in-progress candidates). User confirms, adjusts, adds extras, and provides "Tomorrow I start with". Marks confirmed tasks as done, regenerates dashboard, fills End of Day journal sections.
  - Updated description and intro to reflect three modes.
  - Updated Speed Principles: added max 2 round-trips for update/close modes.

---

## 2026-03-13 — Documentation audit and accuracy fixes

### Updated

- `README.md`
  - Architecture diagram: added `/task`, `/task-review`, and `Tasks` to the skill and vault lists
  - "What's Included": expanded from 4 to 12 ready-to-use skills with accurate descriptions; removed all Todoist references from skill descriptions
  - Vault structure: expanded to reflect full folder layout (Tasks/, Inbox/, Thoughts/, Ideas/, Archive/, workspace_changelog.md)
  - Quick Start: removed stale `claude mcp add todoist` line
  - `/weekly-plan` idea: removed "Todoist sync" reference
  - Templates description: updated to mention all 5 templates including ideas and tasks
- `CLAUDE.md`
  - Folder structure: added `.claude/skills/shared/` (shared utilities), `Tomorrow.md`, `Yesterday.md`, and `workspace_changelog.md`
- `.github/copilot-instructions.md`
  - Folder structure: added `Tomorrow.md`, `Yesterday.md`, and `workspace_changelog.md`
  - Task Cadence table: corrected column header from "Prompt files to use" to "Skills to use"

---

## 2026-03-12 — Change 3: Task Tracking System

### Created

- `Tasks/` — individual task files (`T-NNN - Title.md`) with YAML frontmatter (id, status, priority, owner, deadline, source, type, created) and a Changelog section for audit trail
- `Tasks/_counter.md` — auto-increment counter (`next_id: 1`) for task IDs
- `Dashboard/Tasks.md` — generated active task dashboard with checkboxes grouped by status (In Progress, Backlog, Inbox, Blocked). Checking a box in Obsidian marks the task done on next skill run.
- `templates/task-note.md` — template for individual task files
- `.claude/skills/task/SKILL.md` — `/task` skill: low-friction CRUD for tasks — create, update status/fields, add comments, query single or list, batch create from action items. Handles dashboard regeneration and checkbox sync.
- `.claude/skills/task-review/SKILL.md` — `/task-review` skill: ad-hoc triage and prioritization. Phases: sync checkboxes → overview → triage inbox → review blocked → review stale → reprioritize → finalize. Supports focus modes (full, inbox, blocked, priorities, cleanup).

### Updated

- `.claude/skills/today/SKILL.md`
  - Phase 1a: reads task files + syncs dashboard checkboxes
  - Phase 2a: shows tasks grouped by status instead of P-Tasks
  - Phase 3: focus items use task wikilinks (`[[T-NNN - Title]]`)
  - Phase 4: updates task statuses and regenerates dashboard
  - Phase 5: summary uses task format
- `.claude/skills/meeting/SKILL.md`
  - Step 3: searches `Tasks/` instead of P-Tasks for duplicates
  - Step 6: creates task files via `/task create` workflow instead of just checkboxes
  - Output: references task IDs with wikilinks
- `CLAUDE.md`
  - Folder structure: replaced `Weekly P-Tasks.md` with `Tasks.md`, added `Tasks/` folder
  - Naming conventions: added task file row, replaced P-Tasks system section with full Task Tracking System section
  - Task Cadence: changed weekly → ad-hoc with `/task-review`
  - Skills table: added `/task` and `/task-review`
  - Key Files: replaced `Weekly P-Tasks.md` with `Tasks.md` and `Tasks/_counter.md`
- `.github/copilot-instructions.md`
  - Same pattern of updates as `CLAUDE.md` (folder structure, naming conventions, task tracking, cadence, skills, key files)

### Archived

- `Dashboard/Weekly P-Tasks.md` → `Archive/Notes Archive/Weekly P-Tasks.md` (replaced by task tracking system)

---

## 2026-03-12 — Change 2: Creative & Strategy Skills (Thoughts, Ideas, Develop, Synthesize)

### Created

- `Thoughts/` — monthly rolling log of raw, unpolished thoughts. One file per month (`YYYY-MM.md`). Entries are dated blocks; original captures are immutable; follow-up context is added as annotations (`> **date**: ...`) without altering the original.
- `Thoughts/README.md` — explains the folder, format, annotation pattern, and lifecycle
- `Ideas/` — tracked idea files for thoughts that have been promoted and are worth developing. One file per idea.
- `Ideas/README.md` — explains what belongs here, naming convention, and lifecycle statuses
- `templates/idea-note.md` — template for idea files with frontmatter (`status`, `themes`), sections (The Idea, Why This Matters, Evidence & Signals, Related, Progress Log, Next Step), and Source field
- `.claude/skills/thought/SKILL.md` — `/thought` skill: appends to `Thoughts/YYYY-MM.md`, searches vault for conceptual (not keyword) connections, supports annotation mode (`/thought annotate`), quick-capture path for single sentences
- `.claude/skills/idea/SKILL.md` — `/idea` skill: creates idea file from scratch or promotes a thought; reads full vault to find related content by context and implication; themes optional; marks thought as promoted with annotation
- `.claude/skills/ideas-review/SKILL.md` — `/ideas-review` skill: reads all ideas and thoughts log, clusters by inferred themes (not just tags), flags stale ideas, surfaces promotable thoughts, identifies cross-idea tensions and merge candidates, supports bulk status/theme updates
- `.claude/skills/develop/SKILL.md` — `/develop` skill: sparring partner and Socratic stress-tester; builds internal model of idea's core assertion and assumptions; asks one probing question at a time; plays devil's advocate; helps complete empty sections; writes session log back to idea file; updates status
- `.claude/skills/synthesize/SKILL.md` — `/synthesize` skill: turns a theme or set of ideas into a Plan, Narrative, Strategy brief, or Decision; reads full vault for context; warns if ideas are not yet developed; names tensions rather than papering over them; saves output to `Loose Notes/Work/`; updates source idea Progress Logs

### Updated

- `CLAUDE.md`
  - Folder structure: added `Thoughts/` and `Ideas/`; updated `Loose Notes/Work/` description to include synthesized outputs
  - Naming conventions: added rows for thought log and idea files
  - Tags: added `Idea`
  - Available Skills table: added `/thought`, `/idea`, `/ideas-review`, `/develop`, `/synthesize`
- `.github/copilot-instructions.md`
  - Same updates as `CLAUDE.md` (folder structure, naming conventions, tags, skills table)

---

## 2026-03-12 — Change 1: Hybrid Inbox + Archiving System

### Fixed
- Renamed `Archive/Metings Archive/` → `Archive/Meetings Archive/` (typo, existing files preserved)

### Created
- `Inbox/` — drop zone for unstructured content with no home yet (braindumps, pasted threads, voice notes, fragments). No classification required at capture time.
- `Inbox/README.md` — explains what belongs in Inbox, what doesn't, and how the flow works
- `.claude/skills/process-inbox/SKILL.md` — new `/process-inbox` skill: lists Inbox files, classifies each (meeting note / decision / idea seed / reference / braindump / task list), creates a structured output in the right location, and moves originals to `Archive/Notes Archive/`

### Updated
- `.claude/skills/meeting/SKILL.md`
  - Added Step 3.5: silently archive raw source file to `Archive/Meetings Archive/[filename]-raw.md` before writing structured note
  - Browse mode now scans both `Meetings/` and `Inbox/` for unprocessed meeting notes
  - Output format now confirms archive path
  - Notes section reinforces archiving as mandatory
- `CLAUDE.md`
  - Folder structure updated: added `Inbox/`, `Archive/Meetings Archive/`, `Archive/Notes Archive/`
  - Naming conventions: added rows for inbox files and archived raw files
  - "How AI Should Work" → Do section: added archiving rules
  - Available Skills table: added `/process-inbox`
- `.github/copilot-instructions.md`
  - Same updates as `CLAUDE.md`
  - Available Skills table was commented out — now uncommented so Copilot in VS Code reads it
