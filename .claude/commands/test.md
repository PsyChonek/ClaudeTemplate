# Run Tests

Run the project test suite with the selected scope.

<!-- TODO: Replace [test commands] below with the actual commands for this project.
     Run @init to auto-detect and fill these in. -->

## Steps

Working directory: `[PROJECT_ROOT]`

### 1. Ask what to test

Use `AskUserQuestion` with these options:

- **All** — run the full test suite
- **Unit only** — run unit tests
- **Integration only** — run integration/e2e tests
- **Watch mode** — run tests in watch mode (re-run on file change)
- **Update tests** — run all tests, then fix any failures

### 2. Execute

**All:**
```
[TODO: full test command, e.g. "cargo test" / "pnpm test" / "pytest" / "dotnet test"]
```

**Unit only:**
```
[TODO: unit test command]
```

**Integration only:**
```
[TODO: integration test command]
```

**Watch mode:**
```
[TODO: watch command, e.g. "pnpm test --watch" / "cargo watch -x test"]
```

**Update tests:**

Run all tests first. For each failing test, examine the failure output and update the test or the production code so it reflects correct behaviour. Re-run after each fix until all tests pass. Do **not** delete tests to make them pass — fix the underlying problem.

### 3. Report

Print a one-line summary per test group:
- ✓ `unit` — 42 passed
- ✓ `integration` — 8 passed
- ✗ `e2e` — 1 failed (test name + error snippet)

If anything fails and you are not in "update" mode, stop and show the full failure output.
