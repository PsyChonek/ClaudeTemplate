# Deploy

Deploy the latest built artifacts to target environments.

<!-- TODO: Replace [deploy commands] below with the actual deployment for this project.
     Run @init to auto-detect and fill these in. -->

## Working directory: `[PROJECT_ROOT]`

---

## Step 1 — Ask target (if multiple)

If the project deploys to more than one environment, ask:

> **Deploy to:**
> `[1] [Environment A]   [2] [Environment B]   [3] All`

---

## Step 2 — Deploy

Run all deployments **simultaneously** where possible.

### [Environment A] (e.g., local / staging / production)

```bash
[TODO: e.g. "scripts/deploy.sh"
            "scp target/release/server user@host:/tmp/ && ssh user@host '...'"
            "kubectl apply -f k8s/"
            "fly deploy"
            "vercel --prod"]
```

### [Environment B] (e.g., remote server)

```bash
[TODO: e.g. "scp build/app user@host:/tmp/app && ssh user@host 'sudo systemctl restart app'"]
```

---

## Step 3 — Verify

After deploying, verify each target is running correctly:

```bash
[TODO: e.g. "curl -f https://[host]/health"
            "ssh user@host 'systemctl is-active app'"
            "docker inspect --format '{{.State.Status}}' <container>"]
```

If a target fails verification, print the last N lines of its logs and stop.

---

## Output

Report status for each target:
- ✓ [Environment A]: running
- ✓ [Environment B]: running / ✗ failed — [reason + last log lines]
