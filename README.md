# Claude Code Project Template

Structured AI-assisted development for any project — instructions, slash commands, and per-directory coding rules.

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
3. **Run `/claude-template:update`** — it scans your project and sets everything up:
   ```
   /claude-template:update
   ```
4. **Review and approve** each proposal — the skill walks you through interactive choices
5. **Done** — your project now has customized `CLAUDE.md`, `AGENTS.md`, and your selected components

## What `/claude-template:update` does

### First run
- Scans your project to detect stack, commands, conventions
- Generates `CLAUDE.md` and `AGENTS.md` with project-specific content
- Creates per-directory `AGENTS.md` files for key modules
- Walks you through interactive selection of optional components:
  - **Commands** — pick individual: `/build`, `/test`, `/dev`, `/push`, `/bump`, `/release`, `/team`
  - **Skills** — pick individual: build, test, release (with persistent memory)
  - **Teams** — role definitions for parallel teamwork
- Saves your per-item choices to `.claude-template.json`

### Subsequent runs
- Checks upstream for template updates
- Refreshes instructions based on current project state
- Updates installed components, skips declined ones
- Asks only about **new** items added upstream

## Repository structure

```
commands/claude-template:update.md          ← the only exposed skill (/claude-template:update)
template/                   ← payload copied into target projects
  commands/                 ← slash commands
  skills/                   ← skill packages with memory
  teams/                    ← team role definitions
CLAUDE.md                   ← template for project instructions
AGENTS.md                   ← template for root rules
```

## File ownership (in target projects)

- `commands/*.command.md` — edit these directly (source of truth)
- `.claude/commands/*.md` — auto-generated mirrors, do not edit
- `AGENTS.md` files throughout the project — managed by `/claude-template:update`

## Keeping things in sync

Run `/claude-template:update` again to check for upstream updates and refresh instructions.
