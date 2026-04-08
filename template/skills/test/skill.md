# Test Skill

Provides test execution knowledge, helper scripts, and accumulated learnings for the `/test` command.

## Before Testing

1. Read `skills/test/memory.md` for known flaky tests and environment-specific notes
2. Check if any pre-test scripts exist in `skills/test/scripts/` and run them (e.g., test data setup)

## After Testing

If tests fail:
1. Cross-reference failures against known flaky tests in `memory.md`
2. If a failure is new and reveals a pattern, append it to `skills/test/memory.md`

If tests pass with notable observations (slow tests, new coverage gaps):
1. Append to `skills/test/memory.md` under "Learned"

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/setup-test-data.sh` | [TODO: test data seeding / fixture setup] |
| `scripts/cleanup.sh` | [TODO: post-test cleanup — e.g., temp files, test DBs] |

## Test Data

Place test fixtures, mock data, and seed scripts in `skills/test/data/`.
