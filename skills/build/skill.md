# Build Skill

Provides build knowledge, helper scripts, and accumulated learnings for the `/build` command.

## Before Building

1. Read `skills/build/memory.md` for known issues and environment-specific notes
2. Check if any pre-build scripts exist in `skills/build/scripts/` and run them

## After Building

If the build fails:
1. Diagnose the root cause
2. If the fix reveals a recurring pattern, append it to `skills/build/memory.md` under "Known Issues"
3. If the fix is environment-specific, append it under "Environment Notes"

If the build succeeds with notable observations (new warnings, changed timings):
1. Append to `skills/build/memory.md` under "Learned"

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/prebuild.sh` | [TODO: pre-build setup — e.g., env check, dependency sync] |
| `scripts/postbuild.sh` | [TODO: post-build cleanup — e.g., artifact packaging] |

## Templates

| Template | Purpose |
|----------|---------|
| `templates/build-report.md` | [TODO: build report format] |
