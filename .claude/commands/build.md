# Build

Build the project.

<!-- TODO: Replace [build commands] below with the actual commands for this project.
     Run @init to auto-detect and fill these in. -->

## Steps

Working directory: `[PROJECT_ROOT]`

### 1. Ask build type

Use `AskUserQuestion`:
- **Debug** — fast build, includes debug symbols
- **Release** — optimized build for distribution

### 2. Build

**Debug:**
```
[TODO: debug build command, e.g. "cargo build" / "pnpm build:dev" / "dotnet build"]
```

**Release:**
```
[TODO: release build command, e.g. "cargo build --release" / "pnpm build" / "dotnet build -c Release"]
```

If there are multiple targets (e.g., server + client, or multiple platforms), build in the correct dependency order:

1. **[TODO: target A]** — build first (others depend on it)
   ```
   [TODO: command]
   ```

2. **[TODO: target B]** — can build in parallel with target C
   ```
   [TODO: command]
   ```

3. **[TODO: target C]** — can build in parallel with target B
   ```
   [TODO: command]
   ```

Wait for all to complete. If any fails, stop and report the full error output.

### 3. Report

Report ✓ / ✗ for each target with a one-line status.

List any output artifacts produced (paths and descriptions).
