# AI workspace for non-developers

A blueprint for building an AI-powered workspace for non-dev roles with both [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and [Visual Studio Code] with [GitHub Copilot] and [Obsidian](https://obsidian.md/). It can be extended through MCP integrations.

This is not a plugin or an app. It's a structured Obsidian vault with Claude Code/GitHub Copilot skills that turn your notes into an intelligent operating system for non-developer roles, primary product and program managers. Your vault becomes the persistent memory. Claude Code or GitHub Copilot becomes the executor. MCP servers bridge the gap to your tools.

The workspace works well, but there are likely use cases that won't work exactly as expected. If that happens, debug and use your connected LLM model to update the workspace.

Use the models wisely. I use:
- Sonnet as default
- Haiku for simple things like adding new tasks or thoughts
- Opus for complex topics that require high judgement, like developing ideas or making decisions

**How to start**: tl;dr; Clone this repo and start using it. I started simple with /today to help me organize the day, then moved on to meetings and task capture. 

**Background**: This template is inspired by a blog post [Your AI Has No Memory. Mine Does.](https://productpeak.substack.com/p/your-ai-has-no-memory-mine-does). 

**Questions?** Both Slack and email work. If you share it, I'll appreciate a tag or mention.

## The Architecture

```
+-------------------+    +---------------------+    +-------------------+
|                   |    |                     |    |                   |
|  OBSIDIAN VAULT   |<-->|    CLAUDE CODE      |<-->|   MCP BRIDGES     |
|  (persistent      |    |    (executor)       |    |                   |
|   brain)          |    |                     |    |  - GitHub         |
|                   |    |  Skills:            |    |  - [Your tools]   |
|  - Daily journals |    |  /today             |    |                   |
|  - Meeting notes  |    |  /meeting           |    |                   |
|  - Decisions      |    |  /decision          |    |                   |
|  - Loose notes    |    |  /communicate       |    |                   |
|  - Thoughts       |    |  /thought           |    |                   |
|  - Ideas          |    |  /idea              |    |                   |
|  - Tasks          |    |  /ideas-review      |    |                   |
|                   |    |  /develop           |    |                   |
|                   |    |  /synthesize        |    |                   |
|                   |    |  /process-inbox     |    |                   |
|                   |    |  /task              |    |                   |
|                   |    |  /task-review       |    |                   |
|                   |    |                     |    |                   |
+-------------------+    +---------------------+    +-------------------+
        ^                         |
        |       writes back       |
        +-------------------------+
```

**Obsidian** is where your knowledge accumulates - daily journals, meeting notes, decisions, priorities. Everything in Markdown, everything searchable.

**Claude Code** reads from your vault and acts on what it finds. Skills are repeatable workflows for tasks you do frequently: planning your day, processing meeting notes, making decisions, drafting communications.

**MCP servers** let Claude Code reach beyond local files. GitHub access lets it check engineering status. Connect any MCP server to make the system participate in your actual workflow instead of just reading files.

## What's Included

### 12 Ready-to-Use Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **Plan Today** | `/today` | Morning planning, mid-day updates, or end-of-day close: reviews active tasks, sets focus items, guides day wrap-up |
| **Process Meeting** | `/meeting` | Takes your raw meeting notes and structures them: decisions, action items, task creation, context links |
| **Make Decision** | `/decision [topic]` | Gathers context from your vault and GitHub, presents options with trade-offs, creates documented decision |
| **Draft Communication** | `/communicate [topic + audience]` | Adapts tone to audience (engineering/leadership/customers), uses your people profiles |
| **Capture Thought** | `/thought` | Log a fleeting thought or annotate an existing one in the monthly Thoughts log |
| **Create Idea** | `/idea` | Create an idea file or promote a thought from the Thoughts log into a tracked idea |
| **Review Ideas** | `/ideas-review` | Review the idea landscape, find themes, surface promotable thoughts |
| **Develop Idea** | `/develop` | Stress-test and develop an idea — sparring partner, devil's advocate |
| **Synthesize** | `/synthesize` | Turn a cluster of ideas into a plan, narrative, or strategy brief |
| **Process Inbox** | `/process-inbox` | Route unstructured Inbox files into structured notes, then archive originals |
| **Task** | `/task` | Create, update, or query tasks in the task tracking system |
| **Task Review** | `/task-review` | Ad-hoc task triage and prioritization |

### 1 Onboarding Skill

| Skill | Command | What it does |
|-------|---------|-------------|
| **Personalize** | `/personalize` | Interactive setup: configures CLAUDE.md and people profiles for your specific role and company |

### Vault Structure

```
├── Dashboard/             # Living documents (Tasks.md, people-profiles.md)
├── Tasks/                 # Individual task files (T-NNN - Title.md)
├── Ideas/                 # Developed ideas with lifecycle tracking
├── Thoughts/              # Monthly rolling log of raw thoughts (YYYY-MM.md)
├── Inbox/                 # Drop zone for unstructured content
├── journals/              # Daily notes (YYYY/MM-Month/DD-MM-YYYY.md)
├── Meetings/              # Meeting notes
├── Loose Notes/Work/      # Decisions, drafts, analysis
├── templates/             # Note templates for daily, meeting, loose notes, ideas, and tasks
├── Archive/               # Raw source files before processing
├── .claude/skills/        # Claude Code skills (shared/ for cross-skill utilities)
├── CLAUDE.md              # The "operating system" - tells Claude who you are and how to work
└── workspace_changelog.md # Log of all structural workspace changes
```

## Quick Start

### Prerequisites

- [Obsidian](https://obsidian.md/)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (`npm install -g @anthropic-ai/claude-code`)
- Claude API key or Claude Pro/Max subscription

### Setup (5 minutes)

```bash
# 1. Clone this repo
git clone https://github.com/aleksander-dytko/ai-pm-workspace.git
cd ai-pm-workspace

# 2. Add MCP servers (optional but recommended)
claude mcp add github --transport streamable-http --url https://api.githubcopilot.com/mcp

# 3. Open in Obsidian (Open folder as vault)

# 4. Run Claude Code and personalize
claude
# Then type: /personalize
```

The `/personalize` skill walks you through an interactive setup - it asks about your role, company, tools, and configures everything automatically.

See [setup/SETUP-GUIDE.md](setup/SETUP-GUIDE.md) for detailed instructions.

## How CLAUDE.md Works

`CLAUDE.md` is the heart of this setup. It's a Markdown file that Claude Code reads automatically when you start a session. It contains:

- **Your identity**: Role, company, product area
- **Company context**: What your company does, who uses your product
- **Vault conventions**: File naming, folder structure, tags
- **MCP configuration**: Which servers are available and how to use them
- **Skill reference**: What each skill does and when to use it
- **Behavioral instructions**: How Claude should work with your vault

Think of it as the operating system for your AI assistant. The more context you put in, the less you need to explain in each conversation.

## Extending the Workspace

### Build Your Own Skills

Skills are Markdown files in `.claude/skills/[name]/SKILL.md`. Each skill has:

1. **Frontmatter**: Name and description (how Claude discovers the skill)
2. **Workflow**: Step-by-step instructions for Claude to follow
3. **Output format**: What the result should look like

Ideas for skills you can build:

- **`/weekly-plan`**: Sunday/Monday weekly planning with P-Tasks and overplanning detection
- **`/initiative`**: Track product initiatives by pulling status from external repos
- **`/reflect`**: Monthly personal reflection agent that analyzes mood/energy patterns from your journals

### Add MCP Integrations

Any MCP-compatible tool can be added:

```bash
claude mcp add [name] --transport streamable-http --url [url]
```

Examples: Notion, Linear, Jira, Confluence, your company's internal docs API.

### Customize Templates

Edit files in `templates/` to match your preferred note structure. Skills reference these templates when creating new notes.

## Philosophy

This workspace is built on three principles:

1. **Context over prompts**: When AI has deep context, the prompt barely matters. A simple "What should we do about X?" becomes a rich interaction because Claude already knows what X is.

2. **Skills over conversations**: Repeatable workflows shouldn't require re-explaining every time. Skills encode your process once and execute it consistently.

3. **Your vault, your memory**: Everything accumulates in Markdown files you own. No vendor lock-in, no cloud dependency for your knowledge. Claude reads what's there and gets smarter as your vault grows.

## Contributing

This is an open template. If you build skills, patterns, or integrations that others could use, PRs are welcome.

## License

MIT
