# [PROJECT_NAME]

## Purpose
[One-liner describing what this project/repository does]

## Setup
- Prerequisites: [runtime/toolchain requirements — e.g., Node 20+, Rust 1.80+, Docker]
- Install: `[dependency install command]`
- Run: `[dev/start command]`
- Build: `[build command]`
- Test: `[test command]`
- Lint: `[lint command if any]`

## Architecture
[2-3 sentence overview of how the system fits together — data flow, layers, key boundaries]

- **[component/layer]**: [what it does] — `[key directory or file path]`
- **[component/layer]**: [what it does] — `[key directory or file path]`
- Entry point: `[main entry file path — e.g., src/main.rs, src/index.ts]`

## Tech Stack
- [Language + version]
- [Framework + version]
- [Key dependency]: [purpose]

## Conventions
- Naming: [specify per context — e.g., PascalCase for types, camelCase for functions, kebab-case for files]
- Error handling: [pattern — e.g., Result<T, E> with anyhow context, throw + catch, error objects]
- Async: [pattern — e.g., async/await, Tokio runtime, goroutines + channels]
- Imports: [ordering — e.g., stdlib → external → internal → relative, one import per line]
- Config: [format — TOML / YAML / JSON / .env]

## Rules
- [Boundary rule — e.g., "DB access only through repository modules in `src/repo/`"]
- [Safety rule — e.g., "Never `unwrap()` outside tests; use `anyhow` context instead"]
- [API rule — e.g., "All endpoints return `ApiResponse<T>` — never raw types"]
- [Dependency rule — e.g., "No new deps without checking bundle size impact"]
- [What never to do — e.g., "Never commit secrets or .env files"]

## Testing
- Location: [alongside code / in `tests/` / in `__tests__/`]
- Run: `[test command]`
- Coverage: `[coverage command or "not enforced"]`
- Required for: [when tests are needed — e.g., "all public functions", "all endpoints", "bug fixes"]

## Git Workflow
- One feature = one commit
- Bug fixes may be grouped
- Commit before moving on
