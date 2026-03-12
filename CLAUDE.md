# Claude Code Memory: AI PM Workspace

## Identity & Context

<!-- PERSONALIZE: Update these fields with your information. Run /personalize to do this interactively. -->

**Owner**: [YOUR NAME], [YOUR ROLE] at [YOUR COMPANY]
**Focus areas**: [YOUR PRODUCT AREAS - e.g., "Core Platform, API strategy, customer onboarding"]
**Vault purpose**: Personal PM workspace - private, never referenced externally

**Language**: English
<!-- If you work in multiple languages, specify rules here. Example:
- English: Work content, meetings, decisions, communications
- [Other language]: Personal reflections, emotional content
- NEVER mix languages within a note
-->

---

## Company Context

<!-- PERSONALIZE: Replace with your company and product context. This helps Claude understand your domain. -->

**What [YOUR COMPANY] does**: [Brief description of what your company does - 1-2 sentences]

**Target customers**: [Who your customers are - e.g., "Enterprise SaaS companies", "SMB retailers"]

**Core products**:
- **[Product 1]**: [Brief description]
- **[Product 2]**: [Brief description]

**Target users**:
- **[User type 1]**: [Brief description]
- **[User type 2]**: [Brief description]

---

## MCP Servers Available

<!-- These are configured via `claude mcp add` command, not stored in this file. -->
<!-- See setup/mcp-configs/ for setup instructions for each MCP server. -->

You have access to MCP servers configured in your Claude Code environment:

### GitHub MCP
**Use for**: Repository and issue management
**Key repos**:
- **[YOUR REPO]**: [Description - e.g., "Epic tracking issues"]

**Key operations**:
- Check implementation status on epics
- Read engineering comments on issues
- Find related PRs

<!-- OPTIONAL MCPs - uncomment and configure if you use these:

### [Domain Docs MCP]
**Use for**: Product documentation lookup
**Key operations**:
- Understanding current product behavior
- Referencing APIs and features

### Notion MCP
**Use for**: Personal/private notes
**Restriction**: Personal reflection only, never in work skills

-->

---

## Vault Structure & Conventions

### Folder organization

```
/
├── Dashboard/             # Living documents (updated frequently)
│   ├── Tasks.md           # Generated active task dashboard (checkboxes)
│   └── people-profiles.md # Stakeholder communication profiles
├── Tasks/                 # Individual task files (T-NNN - Title.md)
│   └── _counter.md        # Auto-increment ID counter
├── Inbox/                 # Drop zone for unstructured content (processed with /process-inbox)
├── Thoughts/              # Monthly rolling log of raw thoughts (YYYY-MM.md)
├── Ideas/                 # Developed ideas with lifecycle tracking
├── journals/              # Daily notes (YYYY/MM-Month/DD-MM-YYYY.md)
├── Loose Notes/
│   └── Work/              # Work notes, drafts, decisions, synthesized outputs
├── Meetings/              # Meeting notes
├── templates/             # Note templates
├── Archive/
│   ├── Meetings Archive/  # Raw meeting files before processing
│   └── Notes Archive/     # Raw inbox files before processing
└── CLAUDE.md              # This file
```

### Naming conventions

| Type | Format | Example | Location |
|------|--------|---------|----------|
| Daily note | `DD-MM-YYYY.md` | `14-02-2026.md` | `journals/2026/02-February/` |
| Work note | `YYYY-MM-DD - Title.md` | `2026-02-14 - Decision - API scope.md` | `Loose Notes/Work/` |
| Meeting note | `YYYY-MM-DD - Meeting description.md` | `2026-02-14 - Customer sync.md` | `Meetings/` |
| Inbox file | anything | `2026-02-14 - braindump.md` | `Inbox/` |
| Archived raw file | `[original-filename]-raw.md` | `2026-02-14 - Customer sync-raw.md` | `Archive/[type] Archive/` |
| Thought log | `YYYY-MM.md` | `2026-03.md` | `Thoughts/` |
| Idea file | `YYYY-MM-DD - Title.md` | `2026-03-12 - Agentic explain-ability.md` | `Ideas/` |
| Task file | `T-NNN - Title.md` | `T-001 - Ship API docs.md` | `Tasks/` |

### Task Tracking System

**Storage**: Individual task files in `Tasks/`, generated dashboard in `Dashboard/Tasks.md`

**Statuses**: `inbox` ↔ `backlog` → `in-progress` → `done` | `blocked` (needs reason) | `cancelled` (needs reason) | any state → `inbox` (deprioritize)

**Priority levels**: P1 (critical, max 1) → P2 (high, max 2) → P3 (medium) → P4 (low) → P5 (optional)

**Task fields**: id (auto), status, priority, owner (default: Milan), deadline, source (wikilink), type (task/goal), created date, changelog

**Dashboard** (`Dashboard/Tasks.md`): Auto-generated view of active tasks with checkboxes. Check a box in Obsidian to mark done — next task-aware skill run syncs the change.

**Skills**: `/task` (create, update, query) | `/task-review` (ad-hoc triage and prioritization)

### Tags
- `DailyNote` - Daily journal entries
- `LooseNotes` - General notes
- `MeetingNotes` - Meeting records
- `Idea` - Idea files in `Ideas/`
<!-- Add your own tags: people tags (#PersonName), customer tags (#CustomerName), etc. -->

---

## Task Cadence

| Frequency | Tasks | Skills to use |
|-----------|-------|---------------|
| **Daily** | Morning planning, meeting notes, decisions, communications | `/today`, `/meeting`, `/decision`, `/communicate` |
| **Ad-hoc** | Task review, triage inbox, reprioritize | `/task-review` |
| **Monthly** | Reflection, progress review | Custom agent (build your own) |

---

## People & Communication Context

**Reference**: `Dashboard/people-profiles.md` for communication preferences and working styles of key stakeholders.

<!-- PERSONALIZE: Adjust these audience types to match your organization -->

**Communication tone guidance**:
- **Engineering teams**: Technical, specific, actionable (include links to specs, APIs, issues)
- **Design teams**: User-focused, visual references, customer context
- **Leadership**: Strategic, metrics-driven, concise, aligned with company goals
- **Customers**: Professional, benefits-focused, clear ROI, no internal jargon

**Slack markdown formatting**:
- **Bold**: Use `*text*` (single asterisk), NOT `**text**`
- **Bullets**: Use `-` for bullets
- **Emoji**: Use `:emoji_name:` format

---

## Privacy Rules

1. **NEVER reference this vault** in shared repositories or documents
2. **NEVER commit vault content** to other repos
3. **NEVER expose personal content** in work outputs

---

## Available Skills

| Skill | Command | When to use | MCPs used |
|-------|---------|-------------|----------|
| **Plan Today** | `/today` | Morning daily planning and journal fill | - |
| **Process Meeting** | `/meeting` | Process raw meeting notes into structured notes | - |
| **Process Inbox** | `/process-inbox` | Route unstructured Inbox files into the right place | - |
| **Make Decision** | `/decision` | When making product decisions | GitHub, - |
| **Draft Communication** | `/communicate` | Writing Slack messages, emails, updates | - |
| **Capture Thought** | `/thought` | Log a raw thought or annotate an existing one | - |
| **Create Idea** | `/idea` | Create an idea file or promote a thought to an idea | - |
| **Review Ideas** | `/ideas-review` | Review idea landscape, find themes, surface patterns | - |
| **Develop Idea** | `/develop` | Stress-test and develop an idea — sparring partner | - |
| **Synthesize** | `/synthesize` | Turn ideas into a plan, narrative, or strategy brief | - |
| **Task** | `/task` | Create, update, or query tasks | - |
| **Task Review** | `/task-review` | Ad-hoc task triage and prioritization | - |
| **Personalize** | `/personalize` | Initial setup - customize this workspace to your needs | - |

### Skills you can build (examples in README):

| Skill | Purpose |
|-------|--------|
| `/initiative` | Track initiative status from external repos |
| `/reflect` | Personal reflection agent (privacy-bounded) |

---

## Key Files to Understand

| File | Purpose |
|------|---------|
| `Dashboard/Tasks.md` | Generated active task dashboard with checkboxes |
| `Dashboard/people-profiles.md` | Stakeholder communication profiles and working styles |
| `Tasks/_counter.md` | Auto-increment counter for task IDs |

---

## How AI Should Work with This Vault

### Do
- When analyzing journals, look for **patterns across days**, not individual entries
- Be specific and direct - reference concrete notes and patterns
- When creating notes, follow existing naming conventions and use appropriate templates
- Link new notes in today's journal (`journals/YYYY/MM-Month/DD-MM-YYYY.md`)
- When creating work notes, save in `Loose Notes/Work/` and link from daily note
- **Always archive raw source files before processing** — meeting notes go to `Archive/Meetings Archive/`, inbox files go to `Archive/Notes Archive/`
- When processing content from `Inbox/`, move the original to `Archive/Notes Archive/` after creating the structured output
- **Always log workspace changes to `workspace_changelog.md`** — any time you create/rename/delete folders or files, add/update skills, or change conventions in `CLAUDE.md` or `copilot-instructions.md`, append a dated entry. Detail level: what was fixed, what was created, what was updated (with file paths and a description of what changed).

### Don't
- Don't give generic productivity advice - be specific to documented patterns
- Don't create files outside the established folder structure
- Don't assume empty daily note fields mean a bad day
- Don't write to external repositories from this vault

---

## Daily Notes Structure

Daily notes follow the template in `templates/daily-note.md`:

**Sections**:
- **Yesterday**: What happened, energy tracking
- **Today**: Mood, self-care, focus (max 3 items), tasks
- **Notes**: Links to meeting notes and loose notes created that day
- **End of Day**: What was completed, what to start with tomorrow

**Frontmatter**: Includes `energy` and `mood` fields for quantitative tracking.

---

## Important Patterns

### Overplanning tendency
**Pattern**: Many PMs plan too many tasks for the week, leading to disappointment.
**Solution**: When running weekly planning, if the list has more than 5-7 P-tasks or last week's completion rate was low, **challenge and ask to prioritize**. Remind: "Which 3-5 tasks are truly critical?"

### Meeting note enrichment
**Pattern**: Raw meeting notes need structure, context links, and action items extracted.
**Solution**: `/meeting` skill creates structured notes with decisions, action items, and links to relevant context.

### Decision documentation
**Pattern**: Decisions happen in Slack or meetings but aren't always documented.
**Solution**: `/decision` skill creates structured decision notes, links them in the daily journal.
