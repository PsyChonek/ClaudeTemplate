# Release Skill

Provides release process knowledge, helper scripts, and accumulated learnings for the `/release` command.

## Before Releasing

1. Read `skills/release/memory.md` for known CI/CD issues and past release gotchas
2. Check if any pre-release scripts exist in `skills/release/scripts/` and run them

## After Releasing

If the release fails:
1. Diagnose the root cause
2. Append the failure and fix to `skills/release/memory.md` under "Known Issues"

If the release succeeds:
1. Note any observations (timing changes, new warnings) in "Learned"

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/pre-release-check.sh` | [TODO: pre-release validation — e.g., changelog check, version consistency] |
| `scripts/post-release.sh` | [TODO: post-release tasks — e.g., notifications, doc updates] |

## Checklists

| Checklist | Purpose |
|-----------|---------|
| `templates/release-checklist.md` | [TODO: manual verification steps before cutting a release] |
