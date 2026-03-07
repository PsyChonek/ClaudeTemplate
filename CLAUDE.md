# [PROJECT_NAME]

## Purpose
[One-line description of what this project does and who it's for]

## Architecture
[Describe key components/modules and how they relate. Example:]
- **[module-a]**: [what it does]
- **[module-b]**: [what it does]
- **[module-c]**: [what it does]

## Tech Stack
- [Language + version]
- [Framework + version]
- [Key library]: [purpose]

## Conventions
- [Naming convention for files, exports, types]
- [Error handling pattern]
- [Async pattern if applicable]
- [Serialization/config format]

## Rules
- [Key rule that applies everywhere]
- [Key rule that applies everywhere]

## Testing
- [Where tests live]
- [Test runner and command]
- [What must be tested]

## Git Workflow
- **One feature = one commit**: each distinct feature must be committed separately
- **Bug fixes may be grouped**: related fixes can share a commit
- **Commit before moving on**: always commit completed work before starting the next task

## Agent Parallelism
- **Spawn subagents aggressively**: any task touching independent subsystems must be parallelized
- **Main agent = coordinator**: orchestrates, reviews, and merges — heavy reading happens in subagents
- **Independent edits in parallel**: if two files have no shared dependency, spawn two subagents

---

This project uses per-directory `AGENTS.md` files for coding rules.
When editing files in any directory, walk up the tree and apply ONLY
the `AGENTS.md` files found from the file's location to the project
root. Do NOT read `AGENTS.md` files from unrelated branches of the tree.

All `AGENTS.md` locations:
- `/AGENTS.md`
