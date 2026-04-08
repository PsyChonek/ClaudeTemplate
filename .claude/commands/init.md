# Init / Update

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

## Step 5 — Ask about optional components

Locate the plugin source directory. This is the directory where this command file lives — walk up from this file's location to find the plugin root (it contains `agents/`, `commands/`, `skills/`, `teams/` directories).

Present the user with optional components to install:

```
Optional components to copy into your project:

  [ ] commands/  — slash commands: /build, /test, /dev, /push, /bump, /release, /team
  [ ] agents/    — agent definitions: @init, @instructions-generator
  [ ] teams/     — team role definitions for /team command
  [ ] skills/    — skill packages with persistent memory (build, test, release)

Which would you like to install? [all / none / select individually]
```

Wait for the user's selection.

---

## Step 6 — Copy and customize selected components

For each selected component group, copy files from the plugin source into the project root:

### commands/
Copy all `commands/*.command.md` files. Customize each:
- `push.command.md` — replace working directory with real path
- `test.command.md` — replace `[TODO]` with real test commands
- `build.command.md` — replace `[TODO]` with real build commands
- `team.command.md` — update team name prefix, integration check command
- `bump.command.md` — use real version bump approach
- `dev.command.md` — fill in dev commands, or remove if no dev server
- `release.command.md` — use real CI/CD workflow
- `README.md` — copy as-is

### agents/
Copy `agents/init.agent.md`, `agents/instructions-generator.agent.md`, and `agents/README.md`.

### teams/
Copy `teams/explorer.md` and `teams/test-writer.md`. Update test command placeholder. Create additional role files based on the detected project type (see Step 2 role table). Each role file uses:

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

### skills/
Copy the entire `skills/` directory (build/, test/, release/ with their skill.md and memory.md files, plus README.md).

---

## Step 7 — Sync mirrors

For any copied components, generate the auto-mirrored files:

- `agents/*.agent.md` → `.claude/agents/*.md` (with YAML frontmatter)
- `commands/*.command.md` → `.claude/commands/*.md` (with auto-generated header comment)

Mirror format for agents:
```markdown
<!-- Auto-generated from agents/[name].agent.md — do not edit -->
---
name: "[name]"
description: "[First line of source file after the # heading]"
---

[Full content]
```

Mirror format for commands:
```markdown
<!-- Auto-generated from commands/[name].command.md — do not edit -->

[Full content]
```

---

## Step 8 — Save choices

Write `.claude-template.json` to the project root:

```json
{
  "version": "1.0.0",
  "installed": ["commands", "agents"],
  "declined": ["teams", "skills"],
  "lastSync": "YYYY-MM-DD"
}
```

Where `installed` lists selected components and `declined` lists rejected ones. Set `lastSync` to today's date.

---

## Step 9 — Report

```
Initialized [project name]:

  CLAUDE.md            — filled with project info
  AGENTS.md            — root rules created
  [sub-dir]/AGENTS.md  — [list of per-directory files created]

Components installed:
  commands/  — /build, /test, /dev, /push, /bump, /release, /team
  agents/    — @init, @instructions-generator
  [... only list what was installed]

Components declined:
  teams/, skills/

Saved choices to .claude-template.json
Run this command again to check for updates.
```

---

# Update Mode

## Step 1 — Read choices

Read `.claude-template.json` to determine:
- Which components are **installed** (will be updated)
- Which components were **declined** (will be skipped)
- Last sync date

---

## Step 2 — Check upstream for template updates

Locate the plugin source directory (same as Init Step 5).

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

  abc1234  feat: add new agent
  def5678  fix: improve init detection

Files changed:
  New:      [list]
  Modified: [list]
```

### For installed components
Show changes and ask:
- **Accept** — take upstream version
- **Merge** — keep local, add upstream additions
- **Skip** — keep local as-is

### For declined components
Skip silently — do not ask again.

### For new components (not in installed or declined)
If upstream added a new component category, ask the user:
> "Upstream added new component `[name]`. Install it? [yes / no]"

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

Regenerate auto-mirrored files for all installed components:
- `.claude/agents/` from `agents/*.agent.md`
- `.claude/commands/` from `commands/*.command.md`

Remove orphaned mirrors (source file was deleted).

---

## Step 5 — Update config

Update `.claude-template.json`:
- Update `lastSync` to today's date
- Add any new component choices
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

Mirrors synced:
  .claude/agents/[name].md
  .claude/commands/[name].md

Config: .claude-template.json updated (lastSync: [date])
```
