---
name: personalize
description: Customize this workspace to your role, company, and tools through an interactive setup
---

# Personalize Your Workspace

You are helping the user customize this AI PM Workspace template to their specific role, company, and tools. This is a one-time interactive setup that configures CLAUDE.md, skills, and integrations.

## Input

The user invokes this skill after cloning the repository. No arguments needed.

## Workflow

### Phase 1: Welcome + Core Identity (1 round)

Start with a brief welcome, then ask all core identity questions in ONE AskUserQuestion:

```
Welcome to AI PM Workspace! Let's customize this to your setup.
I'll ask a few questions, then update everything automatically.
```

**Questions (4 in AskUserQuestion)**:

1. **Role**: "What's your role?"
   - Options: "Product Manager", "Senior PM / Group PM", "Head of Product / Director", "Engineering Manager"

2. **Company type**: "What type of company?"
   - Options: "B2B SaaS", "B2C / Consumer", "Enterprise / Platform", "Startup / Early stage"

3. **Team structure**: "Who do you typically communicate with?"
   - multiSelect: true
   - Options: "Engineering teams", "Design teams", "Leadership / Executives", "Customers / External"

4. **Note-taking language**: "What language do you write notes in?"
   - Options: "English only", "Mixed (English work + another language for personal notes)"

### Phase 2: Company & Product Details (1 round)

Build the free-text prompt dynamically based on Phase 1:

```
Now the specifics. Tell me about:

1. **Your name**: What's your full name?
2. **Company name**: What company do you work for?
3. **What it does**: One sentence about what your company does
4. **Your product area**: What part of the product do you own? (e.g., "Core Platform", "Growth", "Developer Tools")
5. **Key products/features**: List 2-3 main products or features (comma-separated)
6. **Target users**: Who uses your product? (e.g., "Developers, DevOps engineers")
```

**If "Mixed" language was selected in Phase 1**, also ask:
```
7. **Personal language**: Which language do you use for personal notes and reflections? (e.g., "Spanish", "German", "Polish")
```

### Phase 3: Tool Setup (1 round)

Ask about MCP integrations:

**Questions (4 in AskUserQuestion)**:

1. **GitHub**: "Do you use GitHub for issue/PR tracking?"
   - Options: "Yes, already set up", "Yes, need help setting up", "No"

2. **Other MCPs**: "Do you have other MCP servers you want to configure?"
   - Options: "No, just GitHub", "Yes, I have documentation/docs MCP", "Yes, I have Notion MCP", "Yes, other"

3. **People profiles**: "Do you want to set up stakeholder communication profiles?"
   - Options: "Yes, I'll add key people now", "Later, skip for now"


### Phase 4: GitHub Configuration (conditional)

**Only if user selected GitHub "Yes, already set up" or "Yes, need help setting up":**

If "need help setting up":
- Guide them through: `claude mcp add github --transport streamable-http --url https://api.githubcopilot.com/mcp`
- Tell them to restart Claude Code after adding the MCP, then run `/personalize` again

If "already set up":
- Ask: "What GitHub repositories are important for your work? List 1-3 repos (owner/name format, e.g., 'myorg/product-tracker')."
- For each repo, ask for a brief description (e.g., "Epic tracking", "Main codebase", "Documentation")
- These will be added to the GitHub MCP section in CLAUDE.md

### Phase 4c: Other MCP Configuration (conditional)

**Only if user selected an option other than "No, just GitHub":**

For each MCP the user mentioned:
- Ask for a name and brief description of what it's used for
- Ask for key operations they use it for
- These will be added to the MCP servers section in CLAUDE.md (uncomment the optional MCP template and fill in)

### Phase 5: People Profiles (conditional)

**Only if user wants to set up profiles:**

Ask: "List 3-5 key people you work with. For each, give me: Name, Role, and how they prefer communication (brief/detailed, technical/strategic)."

Create `Dashboard/people-profiles.md` with the provided information in this format:

```markdown
---
tags: Dashboard
updated: YYYY-MM-DD
---
# People Profiles

Communication preferences and working styles of key stakeholders.

## [Person Name]
- **Role**: [Role]
- **Communication style**: [Brief description]
- **Preferred channel**: [Slack/Email/etc.]
- **Tags**: #[FirstName]
```

### Phase 6: Apply All Changes

Update all files based on collected answers:

**1. CLAUDE.md** — Replace all placeholders:
- `[YOUR NAME]` → user's name from Phase 2
- `[YOUR ROLE]` → from Phase 1 (e.g., "Senior Product Manager")
- `[YOUR COMPANY]` → from Phase 2
- `[YOUR PRODUCT AREAS]` → from Phase 2
- Company context section → fill in from Phase 2 (company description, target customers, products, target users)
- Language section:
  - If "English only": keep as-is
  - If "Mixed": replace the comment block with active rules specifying the personal language from Phase 2
- MCP servers section:
  - GitHub: update `[YOUR REPO]` entries with repos from Phase 4. If multiple repos, add additional entries
  - Optional MCPs: uncomment the optional MCP template and fill in for any MCPs from Phase 4c
  - If a tool was not configured, add a comment: `<!-- Not configured. Run /personalize to set up. -->`
- Communication tone guidance:
  - Keep only the audience types the user selected in Phase 1 ("Who do you typically communicate with?")
  - Remove entries for audiences they don't communicate with
  - If they selected "Customers / External", keep the customer entry; otherwise remove it
- Available Skills table: no changes needed (already correct)

**2. Dashboard/people-profiles.md** — If people provided in Phase 5:
- Write the complete file with all profiles

**3. Dashboard/Weekly P-Tasks.md** — Initialize with current week:
- Set the week header to the current week's date range
- Add empty P1-P5 sections so the user can start planning immediately

**4. templates/daily-note.md** — If "Mixed" language selected:
- Add a comment at the top: `<!-- Language rule: Work sections in English, personal reflections in [language] -->`

### Phase 7: Summary + Next Steps

```
Workspace personalized!

Updated:
- CLAUDE.md (your identity, company context, MCP config)
[if GitHub] - GitHub repos ([N] repositories configured)
[if people] - People profiles ([N] profiles created)
[if mixed lang] - Language rules ([language] for personal, English for work)
- Weekly P-Tasks (initialized for this week)

Your workspace is ready. Here's what to try:

1. Start your day:     /today
2. Process a meeting:  /meeting (write raw notes first, then invoke)
3. Make a decision:    /decision [topic]
4. Draft a message:    /communicate [topic + audience]

Tips:
- The more notes you write, the smarter Claude gets about your context
- Keep meeting notes in Meetings/, decisions in Loose Notes/Work/
- Update people-profiles.md as you learn communication preferences
- Run /personalize again anytime to update your setup

See setup/SETUP-GUIDE.md for advanced configuration.
```

## Notes

- This skill should feel like a friendly onboarding conversation, not a form
- If the user gives brief answers, don't push for more — fill in reasonable defaults
- If an MCP isn't configured yet, provide the setup command and suggest running /personalize again after
- Don't ask about features the user doesn't need — respect "skip" and "later" answers
- Keep the total interaction to 4-5 rounds maximum
- The summary in Phase 7 should only mention items that were actually configured — skip lines for skipped tools
