# Skills

`skills/` contains self-contained skill packages that commands and agents can use.
Unlike commands (which are single-file prompts), skills are **directories** that bundle
a prompt with supporting resources: scripts, persistent memory, templates, and data files.

## Structure

Each skill lives in its own folder:

```
skills/
  build/
    skill.md          ← main prompt (required)
    memory.md         ← persistent learnings from past runs (auto-maintained)
    scripts/          ← helper scripts the skill can execute
    templates/        ← output templates, config stubs, etc.
  test/
    skill.md
    memory.md
    scripts/
  ...
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [build](build/skill.md) | Build knowledge — compiler flags, dependency order, known issues |
| [test](test/skill.md) | Test execution knowledge — flaky tests, coverage gaps, test data |
| [release](release/skill.md) | Release process — CI/CD quirks, deployment checklist, rollback steps |

## How skills relate to commands

Commands (`/build`, `/test`, etc.) are the user-facing interface.
Skills are the knowledge and tooling backing them.

A command may reference a skill's memory and scripts:
```
commands/build.command.md  →  reads skills/build/memory.md for known issues
                           →  runs skills/build/scripts/prebuild.sh
```

## How to add a skill

1. Create `skills/[name]/skill.md` with the skill prompt
2. Optionally add `memory.md`, `scripts/`, `templates/` as needed
3. Reference the skill from a command or agent

## Skill memory

Each skill has a `memory.md` file that accumulates learnings across sessions:
- Build failures and their fixes
- Flaky test patterns
- Environment-specific quirks
- Performance baselines

Memory is written by the skill during execution and read on subsequent runs.
Format:

```markdown
# [Skill Name] Memory

## Learned

- **[date]**: [what was learned and why it matters]
- **[date]**: [what was learned and why it matters]

## Known Issues

- [issue description] — [workaround]

## Environment Notes

- [machine/OS-specific note]
```

## How to edit a skill

Edit files directly in `skills/[name]/`. Skills are not mirrored anywhere —
they are consumed in-place by commands and agents.
