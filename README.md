# Claude Code Project Template

Structured AI-assisted development for any project — agents, slash commands, and per-directory coding rules.

## Setup

### Option A: Install as a Plugin (recommended)

1. **Add the marketplace** in Claude Code:
   ```
   /plugin marketplace add PsyChonek/ClaudeTemplate
   ```
2. **Install the plugin**:
   ```
   /plugin install claude-template@claude-template
   ```
3. **Run the init agent** — it scans your project and fills in all placeholders:
   ```
   @init Run in interactive mode
   ```
4. **Review and approve** each proposal before the agent writes anything
5. **Done** — your project now has customized `CLAUDE.md`, `AGENTS.md`, and all commands

### Option B: Manual copy

1. **Copy** the contents of this folder into your project root (merge with existing files)
2. **Open Claude Code** in the project directory
3. **Run the init agent**:
   ```
   @init Run in interactive mode
   ```
4. **Review and approve** each proposal before the agent writes anything
5. **Done**

## What you get

| Command | Purpose |
|---------|---------|
| `/push` | Commit and push |
| `/test` | Run the test suite |
| `/build` | Build the project |
| `/dev` | Start the hot-reload dev environment |
| `/bump` | Bump the version |
| `/release` | Cut a release (bump → tag → CI → deploy) |
| `/team` | Spawn a parallel agent team for large features |

| Agent | Purpose |
|-------|---------|
| `@init` | One-time setup — run once per new project |
| `@instructions-generator` | Create/update per-directory `AGENTS.md` files |
| `@template-updater` | Sync improvements from this project back to the template |

## File ownership

- `agents/*.agent.md` — edit these directly (source of truth)
- `.claude/agents/*.md` — auto-generated mirrors, do not edit
- `AGENTS.md` files throughout the project — managed by `@instructions-generator`

## Keeping things in sync

Re-run `@instructions-generator Sync only` after adding agents, adding directories, or changing project-wide conventions.
