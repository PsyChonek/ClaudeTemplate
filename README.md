# Claude Code Project Template

Structured AI-assisted development for any project — instructions, agents, slash commands, and per-directory coding rules.

## Setup

### Install as a Plugin

1. **Add the marketplace** in Claude Code:
   ```
   /plugin marketplace add PsyChonek/ClaudeTemplate
   ```
2. **Install the plugin**:
   ```
   /plugin install claude-template@claude-template
   ```
3. **Run the init skill** — it scans your project and sets everything up:
   ```
   /update
   ```
4. **Review and approve** each proposal before the agent writes anything
5. **Done** — your project now has customized `CLAUDE.md`, `AGENTS.md`, and optionally commands, agents, teams, and skills

## What `/update` does

### First run
- Scans your project to detect stack, commands, conventions
- Generates `CLAUDE.md` and `AGENTS.md` with project-specific content
- Creates per-directory `AGENTS.md` files for key modules
- Asks which optional components to install:
  - `commands/` — `/build`, `/test`, `/dev`, `/push`, `/bump`, `/release`, `/team`
  - `agents/` — `@init`, `@instructions-generator`
  - `teams/` — role definitions for parallel teamwork
  - `skills/` — build, test, release with persistent memory
- Saves your choices to `.claude-template.json`

### Subsequent runs (update)
- Checks upstream for template updates
- Refreshes instructions based on current project state
- Syncs mirrors (`.claude/commands/`, `.claude/agents/`)
- Only asks about new components — respects previously declined choices

## Repository structure

```
.claude/commands/update.md    ← the only exposed skill (/update)
template/                   ← payload copied into target projects
  agents/                   ← agent definitions
  commands/                 ← slash commands
  skills/                   ← skill packages with memory
  teams/                    ← team role definitions
CLAUDE.md                   ← template for project instructions
AGENTS.md                   ← template for root rules
```

## File ownership (in target projects)

- `agents/*.agent.md` — edit these directly (source of truth)
- `commands/*.command.md` — edit these directly (source of truth)
- `.claude/agents/*.md` — auto-generated mirrors, do not edit
- `.claude/commands/*.md` — auto-generated mirrors, do not edit
- `AGENTS.md` files throughout the project — managed by `@instructions-generator`

## Keeping things in sync

Run `/update` again to check for upstream updates and refresh instructions.
