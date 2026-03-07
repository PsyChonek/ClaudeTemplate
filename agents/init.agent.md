# Init Agent

You perform a one-time setup of the Claude Code project template by scanning the codebase and customizing all template files for this specific project.

---

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

## Step 2 — Present findings

Present a clear summary to the user before writing anything:

```
Project: [name]
Description: [one-liner]
Tech: [stack]
Build: [command]
Test: [command]
Dev: [command]
Structure:
  [dir/] — [purpose]
  [dir/] — [purpose]

I will customize:
  CLAUDE.md           — project overview + AGENTS.md linker
  AGENTS.md           — root coding rules
  .claude/commands/push.md
  .claude/commands/test.md
  .claude/commands/build.md
  .claude/commands/team.md
  .claude/commands/bump.md
  .claude/commands/release.md

Proceed? [yes / adjust X first]
```

Wait for confirmation before writing anything.

---

## Step 3 — Fill in CLAUDE.md

Overwrite `CLAUDE.md` with real project content. Keep all sections from the template; fill in every `[placeholder]` with accurate information. The AGENTS.md linker block at the bottom must remain intact (the instructions-generator will populate the full path list later — leave just `/AGENTS.md` for now).

Keep the "Agent Parallelism" and "Git Workflow" sections unchanged — they are universal.

---

## Step 4 — Fill in root AGENTS.md

Overwrite `AGENTS.md` with project-specific rules. Be specific and actionable. Max 60 lines. Fill in every `[placeholder]` with real conventions and rules extracted from the codebase. Do NOT write generic advice like "write clean code".

---

## Step 5 — Customize .claude/commands/

Rewrite each command file with project-specific content:

### push.md

Use the generic push workflow. Replace:
- Working directory → real absolute path to project root
- Co-Authored-By author name → `Claude [model]` (use the model you are running on)

### test.md

Replace the `[TODO]` test commands with the real test commands. If there are multiple test runners (e.g., backend + frontend), include all with clear section headers and ask the user which scope to run.

### build.md

Replace `[TODO]` with the real build commands. If there are multiple targets (platforms, debug/release), list all with clear labels. Specify if any ordering constraint exists between build steps.

### team.md

Define roles based on the project's layers. For each distinct layer (backend, frontend, infra, data, tests, etc.):
- Give the role a short slug name (e.g., `backend`, `frontend`, `data`)
- List which files/directories it owns
- List the self-check commands it must run before reporting done (its build/test commands)

Remove the generic placeholder role table and replace with real roles. Update the spawn prompt template with the real project paths, working directory, and self-check commands.

### bump.md

If version files were found: write the command to bump them. If a bump script exists, reference it. If bumping multiple files (e.g., `package.json` + `Cargo.toml`), list them all. If no versioning system exists, write a note explaining the files to edit manually.

### release.md

If CI/CD was found:
- Write the workflow that triggers a release (tag push, workflow dispatch, etc.)
- Include the `gh` CLI commands (or equivalent) to monitor CI
- Include deployment steps if known

If no CI/CD exists: write a manual release checklist (bump → build → tag → push → deploy).

---

## Step 6 — Run instructions-generator

After all files are written, invoke the `instructions-generator` agent:

> "Run in interactive mode"

It will scan the full project tree and propose AGENTS.md files for sub-directories. Present its proposals to the user for review.

---

## Step 7 — Report

List every file created or modified:

```
✓ CLAUDE.md               — filled with [project name] info
✓ AGENTS.md               — root rules created
✓ .claude/commands/push.md
✓ .claude/commands/test.md    — using [test command]
✓ .claude/commands/build.md   — using [build command]
✓ .claude/commands/team.md    — [N] roles: [role list]
✓ .claude/commands/bump.md    — bumps [version files]
✓ .claude/commands/release.md — [CI/CD system or manual]
✓ Sub-directory AGENTS.md: [list of created files]
```

Suggest next steps:
- Review each customized file and adjust anything the agent got wrong
- Run `/push` to commit the setup
