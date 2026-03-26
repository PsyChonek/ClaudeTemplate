# [PROJECT_NAME]

## Purpose
[One-liner describing what this project/repository does]

## Architecture
- **[component]**: [what it does]
- **[component]**: [what it does]

## Conventions
- Naming: [PascalCase / camelCase / snake_case / kebab-case for files and exports]
- Error handling: [pattern used — e.g., Result<T, E>, throw, return error objects]
- Async: [pattern — e.g., async/await, promises, Tokio, goroutines]
- Config format: [TOML / YAML / JSON / .env]

## Tech Stack
- [Language + version]
- [Framework + version]
- [Key dependency]: [purpose]

## Rules
- [Concrete do/don't rule]
- [Concrete do/don't rule]
- [Import/export convention]
- [What never to do here]

## Testing
- Tests live [alongside code / in tests/ directory]
- Run with: `[test command]`
- New [functions / endpoints / components] require tests before committing

## Git Workflow
- One feature = one commit
- Bug fixes may be grouped
- Commit before moving on

## Agent Parallelism
- Spawn subagents for independent subsystems
- Main agent orchestrates and synthesizes — does not do heavy reading directly

## Instructions files 
- Use AGENTS.md instead of CLAUDE.md
