# [PROJECT_NAME]

## Initialization
When this template is first used in a project, run the following analysis before writing any code:

1. **Detect the stack** — scan the repo for package.json, Cargo.toml, go.mod, requirements.txt, pyproject.toml, *.csproj, pom.xml, Gemfile, composer.json, or equivalent. Identify the language, framework, package manager, and runtime.
2. **Check versions** — verify all dependencies and runtimes are on their latest stable versions. Flag anything outdated.
3. **Read docs** — use `context7` MCP to fetch current best practices and project structure guidelines for the detected framework.
4. **Generate stack-specific rules** — based on the detected stack, append a `## Stack-Specific Rules` section to this file covering:
   - **Security**: stack-relevant vulnerabilities (e.g., XSS for React, SQL injection for ORMs, CSRF for web frameworks, deserialization for Java/.NET)
   - **Performance**: common pitfalls for the stack (e.g., N+1 queries for ORMs, bundle size for SPAs, memory leaks for Node, unnecessary re-renders for React)
   - **Patterns**: idiomatic patterns the framework recommends (e.g., server components for Next.js, repository pattern for .NET, composition API for Vue 3)
   - **Tooling**: recommended linters, formatters, and static analysis for the stack (e.g., ESLint + Prettier for JS, rustfmt + clippy for Rust, black + ruff for Python)
5. **Fill in this template** — replace all `[placeholder]` sections above with actual project values.
6. **Commit** — commit the populated AGENTS.md so the team has a shared baseline.

> Re-run this initialization whenever the stack changes significantly (e.g., major framework upgrade, new language added).

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

## Quality & Best Practices
- Always use the **latest stable versions** of languages, frameworks, and packages — never pin to outdated versions without an explicit reason
- Before implementing, **read official documentation** for any library or API you use — prefer docs over assumptions or training data
- Use the `context7` MCP tool to fetch current docs for any library, framework, or SDK
- **Follow established best practices** for the tech stack — idiomatic patterns, recommended project structure, official style guides
- **Review your plan before coding** — outline the approach, identify edge cases, and validate against requirements before writing code
- When adding dependencies, verify they are actively maintained, check latest version, and review changelogs for breaking changes
- Prefer well-supported, community-standard libraries over obscure or abandoned alternatives

## Debugging & Anti-Looping
When a fix attempt fails, **do not retry the same approach**. Follow this escalation:

1. **After 2 failed attempts** at the same problem — stop and re-read the actual error message, logs, and stack trace carefully. State out loud what you think the root cause is before trying again.
2. **After 3 failed attempts** — step back entirely. Re-read the relevant source code from scratch, check docs, and question your assumptions. The bug is likely not where you think it is.
3. **After 4 failed attempts** — escalate to the user with a clear summary:
   - What you tried and why it didn't work
   - What you believe the root cause is now
   - 2-3 alternative approaches you haven't tried yet
   - Ask the user for direction before continuing

**Anti-pattern checklist** (if you catch yourself doing any of these, stop immediately):
- Making the same change with slight variations hoping it will work
- Adding workarounds on top of workarounds instead of fixing the root cause
- Changing code you don't fully understand
- Ignoring error messages and guessing at fixes
- Fixing symptoms instead of diagnosing the actual problem

**Reset techniques** when stuck:
- `git diff` — review all changes since the last working state; revert if they've grown chaotic
- Add targeted logging/print statements to trace actual vs expected behavior
- Reproduce with a minimal example — strip away unrelated code
- Search for the exact error message in docs, issues, or forums
- Consider if the problem is environmental (wrong version, missing config, stale cache)

## Testing
- Location: [alongside code / in `tests/` / in `__tests__/`]
- Run: `[test command]`
- Coverage: `[coverage command or "not enforced"]`
- Required for: [when tests are needed — e.g., "all public functions", "all endpoints", "bug fixes"]

## Git Workflow
- One feature = one commit
- Bug fixes may be grouped
- Commit before moving on

## Worktree Workflow (Always Active)

When working in a git repository and making any code changes (edits, new files, refactors, bug fixes):

1. **Create worktree first**: Before making any code changes, create an isolated worktree:
   - Derive a short kebab-case slug from the task (e.g., `fix-login-bug`, `add-auth`)
   - Run: `git worktree add .claude/worktrees/<slug> -b worktree-<slug> origin/HEAD`
   - Use the worktree directory as working directory for ALL file changes

2. **Do the work**: Complete the task fully in the worktree — write code, run tests, verify

3. **Commit**: Stage and commit all changes with a descriptive message

4. **Merge back**: From the original repo root:
   - `git checkout main` (or whatever the default branch is)
   - `git merge --no-ff worktree-<slug>`
   - If merge conflicts occur: STOP, report the conflict, keep the branch for manual resolution, do NOT force-resolve

5. **Cleanup on success**:
   - `git worktree remove .claude/worktrees/<slug>`
   - `git branch -d worktree-<slug>`

6. **Report**: Summarize what changed and confirm merge + cleanup

**Exceptions — do NOT use worktree for:**
- Read-only tasks (exploring code, answering questions, reviewing)
- Non-git directories
- Tasks that only modify config/docs outside the repo

**Why this approach:**
- Global CLAUDE.md applies to all projects on the machine
- No dependencies, no scripts, no hooks — pure behavioral instructions
- Cross-platform: Claude handles OS differences in git commands
- Always-on: no prefix needed, Claude uses worktree by default for code changes
- Safe: conflicts are never force-resolved

**Verification:**
1. Open Claude Code in any git repo
2. Ask: "fix the typo in README" or any code change task
3. Claude should: create worktree → make changes → commit → merge → cleanup
4. Verify git log shows the merge commit
5. Verify no leftover worktrees in `.claude/worktrees/`
