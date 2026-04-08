# Release

Cut a release: bump version, tag, trigger CI/CD (if configured), and deploy.

<!-- TODO: Replace [CI/CD commands] below with the actual release flow for this project.
     Run @init to auto-detect and fill these in. -->

## Skill

Before starting, read `skills/release/memory.md` for known CI/CD issues and past release gotchas.
After completing, update `skills/release/memory.md` with any new learnings.
Run any scripts in `skills/release/scripts/` as needed (pre-release checks, post-release tasks).

## Arguments

`$ARGUMENTS` format: `[bump-type]`
- **bump-type**: `major`, `minor`, or `patch`. Default: `patch`.

---

## Steps

Execute the following steps **in order**.

---

### Step 0 — Confirm release options

Use `AskUserQuestion` to confirm:
1. **Bump type** (single-select): `patch`, `minor`, `major` — pre-select whichever matches `$ARGUMENTS`.

Wait for confirmation before proceeding.

---

### Step 1 — Run local checks

Before tagging, verify the codebase is clean:

```
[TODO: full test/lint/check command]
```

If any check fails, **stop** and report the failure. Do not proceed until all checks pass.

---

### Step 2 — Bump version

```
/bump $ARGUMENTS
```

This updates all version files and commits the change.

---

### Step 3 — Tag and push

```bash
VERSION=$(git describe --tags --abbrev=0)  # or read from version file
git tag -a "v${VERSION}" -m "Release v${VERSION}"
git push && git push --tags
```

---

### Step 4 — CI/CD (if configured)

**Option A — GitHub Actions:**
```bash
# Verify the workflow triggered:
gh run list --workflow=[TODO: release-workflow.yml] --limit=1

# Watch until complete:
gh run watch
```

If any job fails:
1. Run `gh run view --log-failed` to get failure details
2. Report the error to the user
3. **Stop** — do not proceed to deployment

**Option B — No CI/CD:**
Build locally:
```
/build
```

---

### Step 5 — Deploy (if applicable)

```
[TODO: deployment command or /deploy]
```

---

### Step 6 — Summary

Report:
- New version
- Tag pushed (`v<version>`)
- CI result (✓ all jobs passed / ✗ failed job name) — if CI is configured
- Deployment status
- Release artifacts or URL (if applicable)
