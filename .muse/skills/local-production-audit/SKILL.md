# Production Audit

## Gather cheap local evidence first

- Name the release surface before you read anything else: what ships, where it deploys, and who it serves. Record the deployed URL, the branch, and the artifact.

- Collect git evidence with `muse.exec`: `git status --short --branch`, `git log --oneline --decorate -20`, and the diff stat against the release branch.

- Read package scripts, CI workflows, env documentation, Docker files, and deploy manifests. Confirm the app starts from a clean checkout with documented commands.

- Inspect migrations and the rollback path. Treat a migration without a tested rollback as a risk, not a detail.

- Map the runtime boundaries that exist in the repo: API routes, webhooks, auth middleware, background workers, cron jobs, and scheduled tasks.

- Skip paid and third-party scanners entirely. Local evidence answers this audit under his zero-budget rule.

## Run the risk lenses

- Apply each lens below and record findings under it. Quote file paths and commands so every claim traces to evidence.

### Auth coverage

- Confirm public, authenticated, and admin routes are separated and enforced server side. Flag any sensitive endpoint that trusts the client.

- Verify secrets stay out of client bundles, logs, example output, and committed files. Check rate limits, CORS policy, and upload validation where the app needs them.

- Probe AI or agent surfaces for prompt injection, tool abuse, and untrusted content reaching privileged actions.

### Data integrity

- Verify migrations run forward cleanly and each destructive migration has a staged, tested rollback. Flag backfills and data imports without a recovery plan.

- Confirm retries are idempotent for writes, jobs, and webhook handlers. A retry that double-charges or double-writes is a blocker.

### Payments and webhooks

- Verify webhook signatures before the handler trusts any payload field. Confirm test-mode and live-mode credentials are separated.

- Require idempotency on every payment, subscription, and fulfillment webhook. Cover replay, duplicate delivery, and out-of-order delivery.

### Operations

- Confirm required environment variables are named, validated at startup, and fail fast when missing. Verify a health check proves dependencies are reachable.

- Confirm deploy, rollback, and incident-owner paths are documented. Check that logs are useful without leaking secrets or personal data.

### UX states

- Walk the launch-critical paths on desktop and mobile. Confirm forms submit on mobile without input zoom, overlap, or blocked states.

- Verify loading, empty, error, and permission-denied states tell the user what happened. Confirm a support or recovery path exists when a critical operation fails.

## Turn evidence into a ship or block call

- Score the release from 0 to 100 using evidence only. Call 0 to 49 blocked and refuse to ship until the top risks are fixed.

- Call 50 to 69 risky and allow shipping only behind a small rollout or internal beta. Call 70 to 84 launchable with caveats when the owners accept the listed risks.

- Call 85 to 100 strong when no launch blocker shows in the available evidence.

- Cap the score at 69 when auth is missing on sensitive data, webhooks are not idempotent, migrations cannot run safely, secrets are exposed, or no rollback path exists.

- Cap the score at 84 when CI is not green or the launch-critical path lacks end-to-end coverage. Treat green CI as a signal, never as proof of readiness.

- Lead the report with one sentence: the score, the band, and the top risks to fix. Then list blockers, high-value fixes, evidence checked, evidence missing, and one next action.

## APPENDIX: post-deploy watch

- Watch the deployed URL after every release in a timed window, or as a recurring cron watchdog with `cron.add`. His real surfaces are Cloudflare Workers URLs and release APKs.

- Check five signals on every pass: HTTP status, new console errors, network failures, performance regression against baseline, and content presence on key elements.

- Treat these as critical and raise the alarm at once: HTTP status other than 200, more than 5 new console errors, LCP above 4 seconds, any 5xx from a critical API endpoint, or a static asset returning 4xx or 5xx.

- Treat these as warnings in the report: LCP up more than 500 milliseconds from baseline, CLS above 0.1, new console warnings, response time more than double the baseline, or an unexpected content-type change on a static asset.

- Log minor variance as info only. End the window with a short canary report: per-signal result, baseline, and delta.

- Pair the watch with local-e2e-testing for regression depth and local-browser-qa for the pre-deploy pass. One verifies the release, the others verify the code behind it.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for repo state, PRs, and CI workflow evidence in the audit.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
