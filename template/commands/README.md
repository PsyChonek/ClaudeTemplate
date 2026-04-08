# Commands

`commands/` is the single source of truth for all command/skill prompts.
Tool-specific locations (`.claude/commands/`) are auto-generated mirrors — do not edit them directly.

## Available Commands

| Command | Description |
|---------|-------------|
| [build](build.command.md) | Build the project (debug or release) |
| [bump](bump.command.md) | Bump the project version across all relevant files |
| [dev](dev.command.md) | Start the hot-reload development environment |
| [push](push.command.md) | Stage, commit, and push all current changes |
| [release](release.command.md) | Cut a release: bump, tag, CI/CD, deploy |
| [team](team.command.md) | Coordinate a team of Claude Code teammates |
| [test](test.command.md) | Run the project test suite |

## How to add a command

1. Create `commands/[name].command.md` with the command prompt
2. Run `@instructions-generator Sync only` to generate tool-specific mirrors
3. The command will be available as `/[name]` in Claude Code

## How to edit a command

Edit `commands/[name].command.md` — never edit the auto-generated copies.
Re-run `@instructions-generator Sync only` after editing.
