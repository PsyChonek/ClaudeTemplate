# Update

You initialize or update the Claude Code project template in the current project. You detect whether this is a first run or an update, and act accordingly.

---

## Step 0 — Detect mode

Check if `.claude-template.json` exists in the project root.

- **Exists** → go to [Update Mode](#update-mode)
- **Does not exist** → go to [Init Mode](#init-mode)

---

# Init Mode

## Step 1 — Explore the project

Read as many of the following as exist (in parallel):
- `README.md`, `README.rst`, `README.txt`
- `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `*.sln`, `pom.xml`, `build.gradle`
- Root directory listing (to identify key folders)
- Any existing `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`
- CI/CD files: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`
- Version files: look for version strings in package.json, Cargo.toml, tauri.conf.json, etc.

Extract:
- **Project name** and one-line description
- **Tech stack**: languages, frameworks, key libraries with versions
- **Build commands**: how to compile/build (debug and release if both exist)
- **Test commands**: how to run tests (unit, integration, e2e — list all)
- **Dev/run commands**: how to start the app locally
- **Project structure**: key directories, what each contains, how they relate
- **Conventions**: naming style, error handling, async patterns
- **Working directory**: absolute path to project root
- **Version files**: where version strings live (for `/bump`)
- **CI/CD**: what system is used, what triggers a release

---

## Step 2 — Detect project type

Use the files found in Step 1 to classify the project. Match the **first** row that applies:

| Detected | Project type | Team roles to create |
|---|---|---|
| `Cargo.toml` (workspace) + `tauri.conf.json` | **Rust + Tauri** | rust-backend, tauri-bridge, frontend, test-writer, explorer |
| `Cargo.toml` (workspace, no Tauri) + multiple crates | **Rust workspace** | one role per main crate, test-writer, explorer |
| `Cargo.toml` (single crate, binary) | **Rust app** | backend, test-writer, explorer |
| `package.json` + (`react`/`vue`/`svelte`/`next`/`nuxt`) + no backend folder | **Frontend JS/TS** | components, state, test-writer, explorer |
| `package.json` + backend folder (`server/`, `api/`, `src/routes/`) | **Fullstack Node** | backend, frontend, test-writer, explorer |
| `pyproject.toml` or `requirements.txt` | **Python** | backend, data, test-writer, explorer |
| `*.sln` or multiple `*.csproj` | **.NET** | api, domain, infrastructure, test-writer, explorer |
| `go.mod` | **Go** | backend, test-writer, explorer |
| anything else | **Generic** | backend, frontend, test-writer, explorer |

---

## Step 3 — Present findings

Present a clear summary to the user before writing anything:

```
Project:  [name]
Type:     [detected type]
Tech:     [stack]
Build:    [command]
Test:     [command]
```

Wait for confirmation before proceeding.

---

## Step 4 — Generate instructions (always)

These are always generated regardless of component selection:

### CLAUDE.md
Overwrite `CLAUDE.md` with real project content. Keep all sections from the AGENTS.md template; fill in every `[placeholder]` with accurate information. Keep the "Worktree Workflow", "Debugging & Anti-Looping", and "Git Workflow" sections unchanged — they are universal.

### AGENTS.md
Overwrite root `AGENTS.md` with project-specific content. Fill in every `[placeholder]`. Max 60 lines. Be specific and actionable.

### Per-directory AGENTS.md files
Scan the project tree and create `AGENTS.md` files where appropriate:
- Distinct modules, layers, or domains
- Folders with unique conventions differing from parent
- Folders with 5+ files or 3+ subfolders
- Boundary folders (backend vs frontend, shared vs app)

Do NOT create `AGENTS.md` for:
- Leaf folders covered by parent
- Gitignored or auto-generated folders
- Config-only folders

---

## Step 5 — Interactive component selection

Locate the plugin source directory. This is the directory where this command file lives — walk up from this file's location to find the plugin root. The template payload lives in the `template/` subdirectory (it contains `commands/`, `skills/`, `teams/` directories).

Walk the user through each component category **one at a time** using interactive questions. For each category, list every available item and let the user pick individually.

### 5a — Commands

Ask the user:

> **Commands** — slash commands copied into your project as `commands/*.command.md`
>
> Available:
> 1. `build`   — Build the project (debug or release)
> 2. `test`    — Run the test suite
> 3. `dev`     — Start hot-reload dev environment
> 4. `push`    — Stage, commit, and push changes
> 5. `bump`    — Bump version across all files
> 6. `release` — Cut a release (bump, tag, CI/CD, deploy)
> 7. `team`    — Spawn a parallel agent team
>
> Which commands would you like? [all / none / list numbers, e.g. "1,2,4"]

### 5b — Skills

Ask the user:

> **Skills** — skill packages with persistent memory, copied into `skills/`
>
> Available:
> 1. `build`   — Build knowledge, compiler flags, dependency order
> 2. `test`    — Test execution knowledge, flaky tests, coverage gaps
> 3. `release` — Release process, CI/CD quirks, rollback notes
>
> Which skills would you like? [all / none / list numbers]

### 5c — Teams

Ask the user:

> **Teams** — role definitions for the `/team` command, copied into `teams/`
>
> Base roles (always included if teams selected):
> - `explorer`    — Read-only research and code mapping
> - `test-writer` — Test creation and coverage
>
> Project-specific roles (based on detected type):
> [list the roles from Step 2 detection table]
>
> Install team roles? [all / none / select individually]

Wait for the user's answer at each step before proceeding to the next.

---

## Step 6 — Copy and customize selected components

For each selected item, copy from the plugin `template/` directory into the project root and customize:

### Commands
For each selected command, copy `template/commands/[name].command.md` to `commands/[name].command.md`. Customize:
- `push.command.md` — replace working directory with real path
- `test.command.md` — replace `[TODO]` with real test commands
- `build.command.md` — replace `[TODO]` with real build commands
- `team.command.md` — update team name prefix, integration check command
- `bump.command.md` — use real version bump approach
- `dev.command.md` — fill in dev commands, or skip if no dev server
- `release.command.md` — use real CI/CD workflow

Also copy `template/commands/README.md` if any commands were selected.

### Skills
For each selected skill, copy the entire `template/skills/[name]/` directory to `skills/[name]/`.
Also copy `template/skills/README.md` if any skills were selected.

### Teams
Copy selected role files from `template/teams/` to `teams/`. Update `test-writer.md` with the real test command. Create additional project-specific role files based on the detected project type (see Step 2). Each role file uses:

```markdown
---
model: sonnet
scope: [one-line description]
---

# [Role Name] Teammate

You are the **[role]** teammate for [PROJECT_NAME].

Working directory: `[real absolute path]`

Your tasks (claim them from the task list):
[TASKS]

Your scope:
[list of files/directories this role owns]

Rules:
- Before editing any file, read the AGENTS.md files walking up from that file to the root.
- SendMessage to the lead when you finish all tasks or are blocked.
- Do NOT edit files outside your scope without asking the lead first.

**Mandatory self-checks before reporting done:**
[role-specific build and test commands]

Key paths:
- `[path]` — [what it is]
```

---

## Step 7 — Sync mirrors

For any copied commands, generate `.claude/commands/` mirrors:

```markdown
<!-- Auto-generated from commands/[name].command.md — do not edit -->

[Full content]
```

---

## Step 8 — Save choices

Write `.claude-template.json` to the project root:

```json
{
  "version": "2.2.0",
  "commands": {
    "installed": ["build", "test", "push"],
    "declined": ["dev", "bump", "release", "team"]
  },
  "skills": {
    "installed": ["build", "test"],
    "declined": ["release"]
  },
  "teams": {
    "installed": ["explorer", "test-writer", "backend"],
    "declined": []
  },
  "lastSync": "YYYY-MM-DD"
}
```

Track each item individually per category. Set `lastSync` to today's date.

---

## Step 9 — Report

```
Initialized [project name]:

  CLAUDE.md            — filled with project info
  AGENTS.md            — root rules created
  [sub-dir]/AGENTS.md  — [list]

Commands installed:
  /build, /test, /push

Skills installed:
  skills/build/, skills/test/

Teams installed:
  teams/explorer.md, teams/test-writer.md, teams/backend.md

Declined (won't be asked again):
  commands: dev, bump, release, team
  skills: release

Saved choices to .claude-template.json
Run /update again to check for updates.
```

---

# Update Mode

## Step 1 — Read choices

Read `.claude-template.json` to determine:
- Which individual items are **installed** (will be updated)
- Which individual items were **declined** (will be skipped)
- Last sync date

---

## Step 2 — Check upstream for template updates

Locate the plugin source directory (the `template/` subdirectory of the plugin root).

Check if `upstream` remote exists for `https://github.com/PsyChonek/ClaudeTemplate`:

```bash
git remote -v
```

If not configured, add it:
```bash
git remote add upstream https://github.com/PsyChonek/ClaudeTemplate.git
```

Fetch and compare:
```bash
git fetch upstream master
git log HEAD..upstream/master --oneline --no-merges
```

If there are upstream changes, show a summary:
```
Upstream has [N] new commits since last sync ([lastSync date]):

  abc1234  feat: add new command
  def5678  fix: improve build skill

Files changed:
  New:      [list]
  Modified: [list]
```

### For installed items
Show changes per item and ask:
- **Accept** — take upstream version
- **Merge** — keep local, add upstream additions
- **Skip** — keep local as-is

### For declined items
Skip silently — do not ask again.

### For new items (not in installed or declined)
If upstream added a new command, skill, or team role, present it interactively:

> "Upstream added new command `deploy`. It does: [description]."
> "Install it? [yes / no]"

Update `.claude-template.json` with the user's choice.

---

## Step 3 — Refresh instructions

Re-scan the project and update:
1. `CLAUDE.md` — check for new directories, updated tech stack, etc.
2. Root `AGENTS.md` — refresh if project structure changed
3. Per-directory `AGENTS.md` files — create new ones for new directories, update stale ones, delete unnecessary ones

Present all proposed changes before writing:
```
Instruction updates:

  Keep:    src/api/AGENTS.md (still accurate)
  Update:  src/components/AGENTS.md (new files detected)
  Create:  src/services/AGENTS.md (new module)
  Delete:  src/old-module/AGENTS.md (directory removed)

Proceed? [yes / review individually]
```

---

## Step 4 — Sync mirrors

Regenerate `.claude/commands/` mirrors for all installed commands.

Remove orphaned mirrors (source file was deleted).

---

## Step 5 — Update config

Update `.claude-template.json`:
- Update `lastSync` to today's date
- Add any new item choices (installed or declined)
- Update `version` if upstream template version changed

---

## Step 6 — Report

```
Updated [project name]:

Upstream changes:
  Applied [N] updates from PsyChonek/ClaudeTemplate

Instructions:
  Updated:  src/components/AGENTS.md
  Created:  src/services/AGENTS.md
  Removed:  src/old-module/AGENTS.md

Components updated:
  commands/build.command.md — accepted upstream changes
  skills/test/skill.md — merged

New components:
  commands/deploy.command.md — installed
  skills/monitoring/ — declined

Mirrors synced:
  .claude/commands/[name].md

Config: .claude-template.json updated (lastSync: [date])
```
