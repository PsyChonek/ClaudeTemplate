# Start Agent Team

Coordinate a team of Claude Code teammates to work on the project in parallel.

> **Prerequisite**: Agent teams must be enabled. If not already set, add to `~/.claude/settings.json`:
> ```json
> { "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
> ```

<!-- TODO: Replace the role table and spawn prompt below with project-specific roles.
     Run @init to auto-generate roles from your project structure. -->

---

## Step 1 — Understand the Work

Read the user's request (the argument passed to `/team`, or the current conversation context) to determine:
- What issues/features need to be worked on
- Which layers/modules are affected
- How much parallelism is possible

If no specific task is given, ask: `"What do you want the team to work on?"`

---

## Step 2 — Choose Teammates

Pick roles based on what the work touches. Use only roles that are actually needed.

| Role | Model | Scope |
|------|-------|-------|
| **[TODO: role-a]** | sonnet | [TODO: files/dirs this role owns] |
| **[TODO: role-b]** | sonnet | [TODO: files/dirs this role owns] |
| **[TODO: role-c]** | sonnet | [TODO: files/dirs this role owns] |
| **test-writer** | haiku | Unit tests, integration test stubs |
| **explorer** | haiku | Read-only research — finding relevant code, auditing |

**Team size guideline**: 3 teammates for most tasks; up to 5 for large cross-layer work.
Spawn `explorer` first (haiku, cheap) to map unfamiliar territory before coding teammates start.

---

## Step 3 — Create the Team

```
TeamCreate team-name="[project]-YYYYMMDD"
```

Name it with today's date (e.g., `myproject-20260307`).

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

Spawn each teammate with a role-specific system prompt. Include:
1. Their role and which files/dirs they own
2. The full list of tasks assigned to them
3. Project context (key paths, AGENTS.md locations)
4. Instructions to read AGENTS.md files for any directory they edit
5. Instructions to `SendMessage` to you (the lead) when blocked or done

**Template for spawn prompt:**

```
You are the [ROLE] teammate for [PROJECT_NAME].
Working directory: [PROJECT_ROOT]

Your tasks (claim them from the task list):
[list tasks]

Your scope:
[list files/dirs]

Rules:
- Before editing any file, read the relevant AGENTS.md files walking up from that file to the root.
- SendMessage to the lead when you finish all tasks or are blocked.
- Do NOT edit files outside your scope without asking the lead first.

**MANDATORY self-checks before reporting done:**
[TODO: add role-specific build/test commands here]

Key paths:
[TODO: list important file paths for this project]
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
[TODO: full test/check command]
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
