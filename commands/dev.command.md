# Dev — Start Development Environment

Start the hot-reload development environment.

<!-- TODO: Replace [dev commands] below with the actual commands for this project.
     Run @init to auto-detect and fill these in. -->

## Working directory: `[PROJECT_ROOT]`

---

## Step 1 — Ask target (if multiple exist)

If the project has more than one runnable target (e.g., desktop + mobile, frontend + backend), ask:

> **Which target?**
> `[1] [Target A]   [2] [Target B]   [3] Both`

Otherwise skip and proceed.

---

## Step 2 — Start infrastructure (if needed)

If the project requires backing services (database, message broker, mock API, docker containers), start them first:

```bash
[TODO: e.g. "docker compose up -d"
            "docker compose -f docker/docker-compose.dev.yml up --detach"]
```

Wait for services to become healthy before proceeding. If health checks are available, poll them:

```bash
[TODO: e.g. "docker inspect --format '{{.State.Health.Status}}' <container>"]
```

---

## Step 3 — Start the app

**[Target A]:**
```bash
[TODO: e.g. "pnpm dev"
            "cargo tauri dev"
            "python -m uvicorn app.main:app --reload"
            "dotnet watch run"
            "go run ./cmd/server"]
```

**[Target B] (if applicable):**
```bash
[TODO: e.g. "cargo tauri android dev"]
```

---

## Step 4 — Print connection info

Once started, print the relevant connection details:

```
Dev environment ready:
  URL:    http://localhost:[port]
  [Other relevant details: API base, credentials, ports]
```

---

## Output

Report:
- Which services/targets are running
- Connection URLs and ports
- Any warnings (slow startup, missing env vars, etc.)
