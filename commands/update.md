# Update

You initialize or update the Claude Code project template. You detect the current state and act accordingly — first run sets everything up, subsequent runs refresh and sync.

> **IMPORTANT — Interactive UI rule:** Every time this prompt says to ask the user a question or present choices, you **MUST** invoke the `AskUserQuestion` tool. Never print a text-based menu, numbered list, or ask the user to type a selection. The tool gives the user a proper interactive UI with arrow keys and space bar to select. This applies to ALL user-facing questions throughout this entire flow.
>
> **AskUserQuestion limits:** Max **4 questions** per call, max **4 options** per question. If you need more options, split into multiple `AskUserQuestion` calls. If a dynamic step (e.g., AGENTS.md proposals, instruction updates) produces more than 4 options, batch them into groups of 4 across sequential calls.

---

## Step 0 — Detect mode

Check if `.claude-template.json` exists in the project root.

- **Exists** → [Update Mode](#update-mode)
- **Does not exist** → [Init Mode](#init-mode)

---

# Init Mode

## Step 1 — Deep project exploration

Read as many of the following as exist (**in parallel** for speed):

**Project metadata:**
- `README.md`, `README.rst`, `README.txt`
- `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `*.sln`, `pom.xml`, `build.gradle`, `composer.json`, `Gemfile`

**Configuration:**
- `tsconfig.json`, `vite.config.*`, `webpack.config.*`, `next.config.*`, `nuxt.config.*`
- `.env.example`, `.env.template` (never read `.env` itself)
- `docker-compose.yml`, `Dockerfile`
- `Makefile`, `justfile`, `Taskfile.yml`

**Existing AI instructions:**
- `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.junie/guidelines.md`
- Any `AGENTS.md` files already in the tree

**CI/CD:**
- `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/config.yml`
- Azure DevOps: `azure-pipelines.yml`

**Version files:**
- Look for version strings in package.json, Cargo.toml, tauri.conf.json, pyproject.toml, `*.csproj`, etc.

**Directory structure:**
- Full root listing + one level deep for key directories

Extract and record:
- **Project name** and one-line description
- **Tech stack**: languages, frameworks, key libraries **with exact versions**
- **Build commands**: debug and release if both exist
- **Test commands**: unit, integration, e2e — list all
- **Dev/run commands**: how to start locally
- **Lint/format commands**: if configured
- **Project structure**: key directories, what each contains, how they relate
- **Conventions**: naming style, error handling, async patterns, import ordering
- **Working directory**: absolute path to project root
- **Version files**: where version strings live
- **CI/CD**: system used, triggers, deploy targets
- **Infrastructure**: databases, message queues, caches, external services

---

## Step 2 — Fetch framework docs

Use the `context7` MCP tool to fetch current best practices for the detected framework:

```
resolve-library-id → query-docs
```

Focus on:
- Recommended project structure
- Idiomatic patterns (e.g., server components for Next.js, composition API for Vue 3)
- Common security pitfalls for this stack
- Performance best practices

Use these docs to inform the quality of generated instructions — don't just rely on training data.

---

## Step 3 — Detect project type

Classify the project. Match the **first** row that applies:

| Detected | Project type | Team roles |
|---|---|---|
| `Cargo.toml` (workspace) + `tauri.conf.json` | **Rust + Tauri** | rust-backend, tauri-bridge, frontend, test-writer, explorer |
| `Cargo.toml` (workspace) + multiple crates | **Rust workspace** | one per main crate, test-writer, explorer |
| `Cargo.toml` (single crate) | **Rust app** | backend, test-writer, explorer |
| `package.json` + frontend framework + no backend dir | **Frontend JS/TS** | components, state, test-writer, explorer |
| `package.json` + backend dir (`server/`, `api/`, `src/routes/`) | **Fullstack Node** | backend, frontend, test-writer, explorer |
| `pyproject.toml` or `requirements.txt` | **Python** | backend, data, test-writer, explorer |
| `*.sln` or `*.csproj` | **.NET** | api, domain, infrastructure, test-writer, explorer |
| `go.mod` | **Go** | backend, test-writer, explorer |
| anything else | **Generic** | backend, frontend, test-writer, explorer |

---

## Step 4 — Present findings and confirm

**You MUST use the `AskUserQuestion` tool** — do not print a text summary and ask the user to type. Invoke:

```json
{
  "questions": [
    {
      "question": "I've analyzed your project. Does this look correct?",
      "header": "Project",
      "multiSelect": false,
      "options": [
        {"label": "Looks good", "description": "[Project name] — [type] — [tech stack] — Build: [cmd] — Test: [cmd]"},
        {"label": "Needs adjustment", "description": "I'll tell you what to change"}
      ]
    }
  ]
}
```

Fill in the description with the actual detected values. If user selects "Needs adjustment", ask what to correct and re-analyze.

---

## Step 5 — Generate instructions

These are **always** generated regardless of component selection.

### 5a — Clean up old AI instruction files

Search for non-standard AI instruction files:
- `AI-INSTRUCTIONS.md`, `AI.md`, `.ai-instructions.md`, `COPILOT.md`, `.copilot-instructions.md`

If found, extract any useful rules, then ask the user:
> "Found old AI instruction files: [list]. Migrate useful rules into AGENTS.md and delete them? [yes / no / review first]"

### 5b — CLAUDE.md

If `CLAUDE.md` already has project-specific content (not just placeholders):
- **Preserve** existing content
- **Add** missing sections from the template
- **Update** the AGENTS.md linker block at the bottom

If `CLAUDE.md` is template-only or doesn't exist:
- Generate from scratch with all sections filled in
- Keep "Worktree Workflow", "Debugging & Anti-Looping", and "Git Workflow" sections unchanged — they are universal

Always include the AGENTS.md linker block at the bottom:
```markdown
All `AGENTS.md` locations:
- @AGENTS.md
- @[sub-dir]/AGENTS.md
- [... every AGENTS.md path in the project]
```

### 5c — Root AGENTS.md

Fill in every `[placeholder]` with real values. Max 60 lines. Requirements:
- **Setup**: exact commands — an agent must go from zero to running
- **Architecture**: 2-3 sentence data flow overview + component list with directory paths
- **Rules**: specific and actionable — "never `unwrap()` outside tests" not "write safe code"
- **Testing**: exact test command and when tests are required
- Include a `## Stack-Specific Rules` section based on context7 docs covering:
  - **Security**: stack-relevant vulnerabilities
  - **Performance**: common pitfalls
  - **Patterns**: idiomatic patterns the framework recommends
  - **Tooling**: recommended linters, formatters, static analysis

### 5d — Per-directory AGENTS.md files

Scan the entire project tree. For each directory, evaluate whether it needs its own `AGENTS.md`.

**Create when:**
- Distinct module, layer, or domain (e.g., `src/api/`, `src/components/`, `src/db/`)
- Unique conventions differing from parent
- 5+ files or 3+ subfolders
- Boundary folder (backend vs frontend, shared vs app)

**Do NOT create when:**
- Leaf folder with few files sharing parent's design — put rules in **parent** instead
- Gitignored or auto-generated (`node_modules`, `bin`, `obj`, `.next`, `dist`)
- Parent's AGENTS.md already fully covers this folder
- Config-only folder
- Fewer than 3 files with no unique conventions

**Format for each AGENTS.md:**

```markdown
# [Folder Name]

## Purpose
One-liner describing what this folder/module does.

## Conventions
- Naming: [PascalCase / camelCase / kebab-case]
- Pattern: [e.g., each file exports a single component]
- Error handling: [pattern used here]

## Rules
- [Concrete do/don't rules for editing files here]
- [Import order, export style, test expectations]

## Examples
- `ExampleFile.tsx` — good example of [pattern]
```

Constraints:
- **Under 60 lines** per file
- Be specific, not generic — no "write clean code"
- Do NOT repeat rules already in a parent AGENTS.md
- Only include sections that have meaningful content

**Present all proposals before writing.** You MUST use the `AskUserQuestion` tool — do not print a text list. Invoke with `multiSelect: true`, one option per proposed AGENTS.md file:

```json
{
  "questions": [
    {
      "question": "Which AGENTS.md files should I create?",
      "header": "Instructions",
      "multiSelect": true,
      "options": [
        {"label": "src/api/AGENTS.md", "description": "API routes, controllers, middleware conventions"},
        {"label": "src/components/AGENTS.md", "description": "React component patterns, naming, state management"}
      ]
    }
  ]
}
```

Replace the example options with the actual proposed paths and their purposes.

---

## Step 6 — Interactive component selection

Locate the plugin source directory — walk up from this command file to find the plugin root. Template payload is in the `template/` subdirectory.

**CRITICAL: You MUST use the `AskUserQuestion` tool for this step.** Do NOT print a text menu. Do NOT ask the user to type numbers. You must invoke the tool so the user gets an interactive checkbox UI with arrow keys and space bar.

This requires **two** `AskUserQuestion` calls due to tool limits (max 4 questions, max 4 options each).

**Call 1 — Commands** (adjust options based on detected stack; omit commands that don't apply):

```json
{
  "questions": [
    {
      "question": "Which dev commands would you like to install?",
      "header": "Dev",
      "multiSelect": true,
      "options": [
        {"label": "/build", "description": "Build the project (debug or release)"},
        {"label": "/test", "description": "Run the test suite"},
        {"label": "/dev", "description": "Start hot-reload dev environment"}
      ]
    },
    {
      "question": "Which workflow commands would you like to install?",
      "header": "Workflow",
      "multiSelect": true,
      "options": [
        {"label": "/push", "description": "Stage, commit, and push changes"},
        {"label": "/bump", "description": "Bump version across all files"},
        {"label": "/release", "description": "Cut a release (bump, tag, CI/CD, deploy)"},
        {"label": "/team", "description": "Spawn a parallel agent team"}
      ]
    }
  ]
}
```

**Call 2 — Skills & Teams** (adjust teams options based on Step 3 detection):

```json
{
  "questions": [
    {
      "question": "Which skill packages would you like to install?",
      "header": "Skills",
      "multiSelect": true,
      "options": [
        {"label": "build", "description": "Persistent memory for build issues, compiler flags, dependency order"},
        {"label": "test", "description": "Persistent memory for flaky tests, coverage gaps, test data"},
        {"label": "release", "description": "Persistent memory for CI/CD quirks, rollback notes, deploy issues"}
      ]
    },
    {
      "question": "Which team roles would you like to install?",
      "header": "Teams",
      "multiSelect": true,
      "options": [
        {"label": "explorer", "description": "Read-only research and code mapping (haiku)"},
        {"label": "test-writer", "description": "Test creation and coverage (haiku)"}
      ]
    }
  ]
}
```

For the Teams question, **append** project-specific roles from Step 3 to the options array (max 4 total). For example, for a Fullstack Node project add `{"label": "backend", "description": "Backend API and routes (sonnet)"}` and `{"label": "frontend", "description": "Frontend components and state (sonnet)"}`. If you have more than 4 team roles, split into a separate `AskUserQuestion` call.

Items the user does **not** select are recorded as `declined`.

---

## Step 7 — Copy and customize

For each selected item, copy from plugin `template/` into the project root:

### Commands
Copy `template/commands/[name].command.md` → `commands/[name].command.md`. Customize each:
- Replace `[TODO]` placeholders with real commands from Step 1
- Replace `[PROJECT_ROOT]` with actual path
- Replace `[PROJECT_NAME]` with actual name
- If `dev.command.md` selected but no dev server exists, skip it and move to `declined`

Copy `template/commands/README.md` if any commands were selected.

### Skills
Copy `template/skills/[name]/` → `skills/[name]/` (entire directory with skill.md and memory.md).
Copy `template/skills/README.md` if any skills were selected.

### Teams
Copy base role files from `template/teams/`. Customize `test-writer.md` with real test command.
Generate project-specific roles based on Step 3 detection:

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

## Step 8 — Sync mirrors

Generate `.claude/commands/` mirrors for all installed commands:

```markdown
<!-- Auto-generated from commands/[name].command.md — do not edit -->

[Full content of source file]
```

Delete any orphaned mirrors that no longer have a source file.

---

## Step 9 — Update CLAUDE.md linker block

Regenerate the AGENTS.md location list at the bottom of `CLAUDE.md` with all current paths:

```markdown
All `AGENTS.md` locations:
- @AGENTS.md
- @src/api/AGENTS.md
- @src/components/AGENTS.md
[... every AGENTS.md in the project]
```

---

## Step 10 — Save choices

Write `.claude-template.json`:

```json
{
  "version": "2.4.0",
  "projectType": "Fullstack Node",
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

---

## Step 11 — Report

```
Initialized [project name] ([type]):

Instructions:
  CLAUDE.md                — filled with project info
  AGENTS.md                — root rules + stack-specific rules
  [path]/AGENTS.md         — [purpose]
  ...

Commands:  /build, /test, /push
Skills:    build, test
Teams:     explorer, test-writer, backend

Declined:  commands(dev, bump, release, team) skills(release)

Config saved to .claude-template.json
Run /claude-template:update again to refresh.
```

---

# Update Mode

## Step 1 — Read config and scan

Read `.claude-template.json` for:
- Installed items (will check for updates)
- Declined items (skip silently)
- Last sync date
- Template version at last sync

Also re-scan the project (same as Init Step 1) to detect any changes to structure, tech stack, or commands.

---

## Step 2 — Check for template updates

Locate the plugin `template/` directory. Compare the plugin's current template version (from `.claude-plugin/plugin.json`) against the version stored in `.claude-template.json`.

If versions differ, compare each installed component file against the plugin's template version:

### For each installed command/skill/team
Read both the local file and the template source. If they differ, use `AskUserQuestion` tool (do NOT print text menus):

```json
{
  "questions": [
    {
      "question": "[file] has upstream changes: [summary]. How to handle?",
      "header": "Update",
      "multiSelect": false,
      "options": [
        {"label": "Accept", "description": "Replace local with upstream version (re-customizes placeholders)"},
        {"label": "Skip", "description": "Keep local version as-is"}
      ]
    }
  ]
}
```

### For new items in template (not in installed or declined)
Use `AskUserQuestion` tool with `multiSelect: true` — do NOT print text lists:

```json
{
  "questions": [
    {
      "question": "New components available from upstream. Which to install?",
      "header": "New",
      "multiSelect": true,
      "options": [
        {"label": "[name]", "description": "[what it does]"}
      ]
    }
  ]
}
```

Unselected items go to `declined`.

### For declined items
Skip silently — never re-ask.

---

## Step 3 — Refresh instructions

Re-scan and compare:

### CLAUDE.md
- Check for new directories, changed tech stack, new dependencies
- Update only the sections that are stale; preserve user customizations
- Regenerate the AGENTS.md linker block with current paths

### Root AGENTS.md
- Compare current project state with what's in AGENTS.md
- Update stale sections (e.g., new setup steps, changed commands)
- Refresh stack-specific rules if framework version changed

### Per-directory AGENTS.md
Evaluate every existing AGENTS.md and every directory:
- **Accurate** → keep
- **Stale** (references removed files/patterns) → propose rewrite
- **Missing** (new directory meets creation criteria) → propose creation
- **Unnecessary** (directory removed or now covered by parent) → propose deletion

Present proposals using `AskUserQuestion` tool (do NOT print text lists):

```json
{
  "questions": [
    {
      "question": "Which instruction updates should I apply?",
      "header": "Instructions",
      "multiSelect": true,
      "options": [
        {"label": "src/api/AGENTS.md", "description": "Rewrite — references removed files"},
        {"label": "src/services/AGENTS.md", "description": "Create — new module detected"},
        {"label": "src/old/AGENTS.md", "description": "Delete — directory removed"}
      ]
    }
  ]
}
```

Replace example options with actual proposed changes.

---

## Step 4 — Sync mirrors

Regenerate `.claude/commands/` for all installed commands.
Remove orphaned mirrors.

---

## Step 5 — Save config

Update `.claude-template.json`:
- `lastSync` → today
- `version` → current plugin version
- Add any new installed/declined items
- Update `projectType` if detection changed

---

## Step 6 — Report

```
Updated [project name]:

Template: [old version] → [new version]

Instructions refreshed:
  Updated:  AGENTS.md (new setup step)
  Updated:  src/api/AGENTS.md (new endpoint conventions)
  Created:  src/services/AGENTS.md (new module)
  Removed:  src/old/AGENTS.md (directory removed)
  Kept:     src/components/AGENTS.md (still accurate)

Components updated:
  commands/build.command.md — accepted upstream
  skills/test/skill.md — accepted upstream

New components:
  /deploy — installed
  /monitor — declined

Mirrors synced: .claude/commands/

Config: .claude-template.json updated
```
