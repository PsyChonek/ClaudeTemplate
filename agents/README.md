# Agents

`agents/` is the single source of truth for all agent prompts.
Tool-specific locations (`.claude/agents/`, `.github/copilot/agents/`, `.junie/agents/`) are auto-generated mirrors — do not edit them directly.

## Available Agents

| Agent | Description |
|-------|-------------|
| [apply-template](apply-template.agent.md) | Applies this template to an existing project — copies to temp folder, interactively merges, cleans up |
| [init](init.agent.md) | One-time setup — scans codebase, detects project type, customizes all template files and creates `teams/` roles |
| [instructions-generator](instructions-generator.agent.md) | Manages the full AGENTS.md system across the codebase |
| [sync-upstream](sync-upstream.agent.md) | Pulls latest changes from upstream `PsyChonek/ClaudeTemplate` and interactively merges |
| [template-updater](template-updater.agent.md) | Syncs improvements from this project back to the Claude Code template |

## How to add an agent

1. Create `agents/[name].agent.md` with the agent prompt
2. Run `@instructions-generator Sync only` to generate tool-specific mirrors
3. The agent will be available as `@[name]` in Claude Code

## How to edit an agent

Edit `agents/[name].agent.md` — never edit the auto-generated copies.
Re-run `@instructions-generator Sync only` after editing.

## Commands (commands/)

`commands/` is the single source of truth for all command prompts (the `/name` interface).
`.claude/commands/` files are auto-generated mirrors — do not edit them directly.
See [`commands/README.md`](../commands/README.md) for the full list and how to add/edit commands.

## Skills (skills/)

`skills/` contains self-contained skill packages that commands and agents can use.
Unlike commands (single-file prompts), skills are **directories** that bundle a prompt
with scripts, persistent memory, templates, and data files.
Commands reference their backing skill via a `## Skill` section.
See [`skills/README.md`](../skills/README.md) for the full structure and how to add skills.

## Team roles (teams/)

`teams/` contains role definition files used by the `/team` command.
Two roles are always present: `explorer.md` and `test-writer.md`.
The rest are created by `@init` based on the detected project type.

To add a new role, create `teams/[role].md` with this frontmatter:
```yaml
---
model: sonnet   # or haiku for cheap/read-only roles
scope: [one-line description shown in the role table]
---
```
Followed by the full spawn prompt for that role.
