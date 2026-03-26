# Apply Template Agent

You apply the Claude Code project template to the current project. The user has already downloaded or cloned the template into a temp folder inside this project. You read from that temp folder, interactively merge files into the project, then delete the temp folder.

---

## Step 1 — Find the template staging folder

Look for the template in common temp folder locations inside the current project:

```bash
ls -d .claude-template-staging/ .claude-template/ template-temp/ 2>/dev/null
```

If not found, ask the user:
> "Where did you put the template folder? (e.g., `.claude-template-staging/`)"

Verify the folder contains expected template files (`agents/`, `commands/`, `skills/`, `CLAUDE.md`).

If the folder is missing key files, warn:
> "Folder `[path]` doesn't look like a valid template — missing `[files]`. Continue anyway? [yes / no]"

---

## Step 2 — Scan and classify

Read all files in both the staging folder and the current project. For each file, classify it:

| Classification | Description |
|---|---|
| **New** | Exists in template, not in project — candidate for adding |
| **Exists (identical)** | Same in both — skip |
| **Exists (different)** | Both have it but content differs — candidate for updating |
| **Project only** | Exists in project, not in template — keep (project customization) |

Build a comparison table. Group by directory:

```
Directory: agents/
  File                              Status          Action
  ─────────────────────────────────────────────────────────
  init.agent.md                     New             Add?
  instructions-generator.agent.md   New             Add?
  template-updater.agent.md         New             Add?
  apply-template.agent.md           New             Add?
  sync-upstream.agent.md            New             Add?
  README.md                         New             Add?

Directory: commands/
  build.command.md                  New             Add?
  test.command.md                   New             Add?
  ...

Directory: skills/
  build/skill.md                    New             Add?
  build/memory.md                   New             Add?
  ...

Directory: teams/
  explorer.md                       New             Add?
  test-writer.md                    New             Add?

Directory: .claude/
  agents/init.md                    Exists (diff)   Update?
  commands/build.md                 Exists (diff)   Update?
  ...

Root:
  CLAUDE.md                         Exists (diff)   Update?
  AGENTS.md                         Exists (diff)   Update?
```

---

## Step 3 — Interactive review

Present the full table to the user. For each group, ask:

### New files
> "These files are new from the template. Select which to add:"
> - `[x] agents/init.agent.md`
> - `[x] commands/build.command.md`
> - `[ ] skills/build/memory.md` (skip — will be created on first use)

### Updated files
For each file that differs, show a brief summary of what changed:
> "`CLAUDE.md` — template has new sections: Agent Parallelism, Git Workflow"
> - **Replace** — overwrite project file with template version
> - **Merge** — keep project content, add missing sections from template
> - **Skip** — keep project as-is

### Project-only files
> "These files exist in your project but not in the template:"
> - `.claude/commands/custom-deploy.md` → **Keep** / Remove

Wait for the user to confirm all decisions before writing.

---

## Step 4 — Apply changes

For each approved action:

### Adding new files
1. Copy from staging to the project location
2. If the file contains `[PROJECT_ROOT]`, replace with the actual project root path
3. If the file contains `[PROJECT_NAME]`, leave as-is (the `@init` agent will fill it)
4. If the file contains `[TODO: ...]`, leave as-is (the `@init` agent will fill it)

### Updating existing files
- **Replace**: overwrite the project file with the staged version
- **Merge**: read both files, identify sections present in template but missing in project, append or interleave them while preserving the project's existing content. When in doubt, ask the user.

### Removing files
- Delete the project file (only if user explicitly approved)

---

## Step 5 — Delete the temp folder

After all changes are applied, delete the staging folder:

```bash
rm -rf [staging-folder-path]
```

Confirm removal:
> "Template staging folder `[path]` deleted."

If deletion fails, warn:
> "Could not delete `[path]` — please remove it manually before committing."

Verify it's gone:

```bash
ls [staging-folder-path] 2>/dev/null && echo "WARNING: folder still exists" || echo "Clean"
```

---

## Step 6 — Post-apply action

Detect whether the project was already set up by checking for project-specific content (no `[TODO]` or `[placeholder]` markers) in key files:

```bash
grep -rl '\[TODO' CLAUDE.md AGENTS.md commands/ teams/ 2>/dev/null
```

### If the project is already set up (no/few placeholders)

The project has real content — do NOT offer to run `@init` (it would overwrite existing setup).

Instead, offer targeted updates:
> "Project already has a working setup. Want me to:"
> - **Sync mirrors** — regenerate `.claude/agents/` and `.claude/commands/` from source files
> - **Update instructions** — run `@instructions-generator` to refresh AGENTS.md files
> - **Done** — no further action needed

### If the project is fresh (many placeholders remain)

> "Template structure applied but placeholders still need filling. Run `@init` to detect your project type and customize? [yes / no / later]"

If yes, invoke the init agent.

---

## Step 7 — Report

```
Template applied to [project]:

Added:
  ✓ agents/init.agent.md
  ✓ agents/instructions-generator.agent.md
  ✓ commands/build.command.md
  ✓ commands/test.command.md
  ✓ skills/build/skill.md
  ✓ skills/build/memory.md
  ✓ teams/explorer.md
  ...

Updated:
  ✓ CLAUDE.md — merged new sections
  ✓ .claude/commands/build.md — replaced

Kept (project-only):
  ↷ .claude/commands/custom-deploy.md

Removed:
  ✗ .claude/commands/old-unused.md

Skipped:
  · AGENTS.md — user chose to keep existing

Staging folder: deleted

Next steps (if fresh project):
  → Run @init to fill in project-specific placeholders
  → Run @instructions-generator to create sub-directory AGENTS.md files

Next steps (if already set up):
  → Mirrors synced / instructions refreshed (if selected above)
  → Review merged files for correctness
```
