# Start Agent Team

Coordinate a team of Claude Code teammates to work on the project in parallel.

> **Prerequisite**: Agent teams must be enabled. If not already set, add to `~/.claude/settings.json`:
> ```json
> { "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
> ```

---

## Step 1 — Understand the Work

Read the user's request (the argument passed to `/team`, or the current conversation context) to determine:
- What issues/features need to be worked on
- Which layers/modules are affected
- How much parallelism is possible

If no specific task is given, ask: `"What do you want the team to work on?"`

---

## Step 2 — Choose Teammates

Read all files in the `teams/` folder. Each file defines one role — its `model` and `scope` are in the YAML frontmatter at the top.

Build the role table from those files:

| Role | Model | Scope |
|------|-------|-------|
| *(read from teams/*.md frontmatter)* | | |

**Team size guideline**: 3 teammates for most tasks; up to 5 for large cross-layer work.
Always include `explorer` (haiku, cheap) when the affected area is unfamiliar — spawn it first.

Pick only roles whose scope overlaps with the current work.

---

## Step 3 — Create the Team

```
TeamCreate team-name="[project]-YYYYMMDD"
```

---

## Step 4 — Break Work into Tasks

Create tasks before spawning teammates. Each task must:
- Touch only one layer/module (no cross-layer tasks)
- Have a clear deliverable ("implement X", "write tests for Y", "fix bug Z")
- List its blockers if order matters

**Task size**: roughly 30–90 minutes of work.

```
TaskCreate title="..." description="..." assignee="[role]"
TaskCreate title="..." description="..." assignee="[role]"
```

---

## Step 5 — Spawn Teammates

For each teammate, read the corresponding `teams/[role].md` file and use it as the spawn prompt. Substitute `[TASKS]` with the actual task list assigned to that role.

```
SendMessage to teammate: [full content of teams/[role].md with [TASKS] filled in]
```

---

## Step 6 — Monitor and Coordinate

While teammates work:
- Check the task list periodically: `TaskList`
- Read teammate messages as they arrive
- If a teammate is blocked on another's output, relay via `SendMessage`
- Update tasks if scope changes: `TaskUpdate`
- Redirect teammates who are going off-track

**Do NOT** do implementation work yourself while teammates are running.

---

## Step 7 — Integration (MANDATORY before shutdown)

When all tasks show `completed`, run the full check suite from the lead session:

```
[TODO: full test/check command — filled in by @init]
```

**If anything fails**: keep the relevant teammate alive, send them the failure output, wait for the fix, then re-run. Only proceed when all checks pass.

After checks pass:
- Review `git diff --stat` to confirm scope
- `/push` when satisfied

---

## Step 8 — Clean Up

```
TeamDelete team-name="[project]-YYYYMMDD"
```

Always shut down teammates before deleting the team.
