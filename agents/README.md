# Agents

`agents/` is the single source of truth for all agent prompts.
Tool-specific locations (`.claude/agents/`, `.github/copilot/agents/`, `.junie/agents/`) are auto-generated mirrors — do not edit them directly.

## Available Agents

| Agent | Description |
|-------|-------------|
| [init](init.agent.md) | One-time project setup — scans codebase and customizes all template files |
| [instructions-generator](instructions-generator.agent.md) | Manages the full AGENTS.md system across the codebase |
| [template-updater](template-updater.agent.md) | Syncs improvements from this project back to the Claude Code template |

## How to add an agent

1. Create `agents/[name].agent.md` with the agent prompt
2. Run `@instructions-generator Sync only` to generate tool-specific mirrors
3. The agent will be available as `@[name]` in Claude Code

## How to edit an agent

Edit `agents/[name].agent.md` — never edit the auto-generated copies.
Re-run `@instructions-generator Sync only` after editing.
