# Bump Version

Bump the project version across all relevant files.

<!-- TODO: Replace [version files] and [bump command] below with the actual approach for this project.
     Run @init to auto-detect and fill these in. -->

## Arguments

`$ARGUMENTS` should be `major`, `minor`, or `patch`. Default to `patch` if omitted.

## Steps

Working directory: `[PROJECT_ROOT]`

### Option A — Bump script exists

```
[TODO: e.g. "pwsh -File scripts/bump-version.ps1 $ARGUMENTS"
            "node scripts/bump.js $ARGUMENTS"
            "python scripts/bump.py $ARGUMENTS"]
```

Report the old version → new version from the script output.

### Option B — No bump script (manual)

Update the version string in each of these files:

| File | Field | Example |
|------|-------|---------|
| `[TODO: e.g. package.json]` | `version` | `"1.2.3"` |
| `[TODO: e.g. Cargo.toml]` | `version` | `version = "1.2.3"` |
| `[TODO: e.g. pyproject.toml]` | `version` | `version = "1.2.3"` |

Compute the new version by incrementing the appropriate component of the current version per `$ARGUMENTS`:
- `patch` → 1.2.3 → 1.2.4
- `minor` → 1.2.3 → 1.3.0
- `major` → 1.2.3 → 2.0.0

After updating all files, commit:
```
git commit -m "chore: bump version to v<new-version>"
```

Report old → new version.
