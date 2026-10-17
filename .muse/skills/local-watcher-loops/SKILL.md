# Watcher Loops

Run Osh's autonomous loops with the discipline they need to survive unattended: every tick idempotent, every cursor persisted, every failure observable.

## Decide Whether a Loop Earns Its Place

- Build a loop only when the work repeats on a schedule, can be verified by a machine, fits the $0 budget, and the tools can both act and observe the result.
- Reject anything one-off. Do it by hand instead.
- Define a done criterion a machine can judge yes or no. Pair it with boundary conditions naming what the loop must never do. Boundaries stop the loop from gaming its own metric.
- Cap retries at a fixed number and name the human who gets escalated past the cap.
- Keep judgment human. The loop proposes and executes. Acceptance stays with a person. Responsibility never transfers to the loop.

## Build Each Tick as Claim, Do, Fulfill

- Treat every cycle as an independent tick. Claim a work unit, do the work, then mark it fulfilled or release it.
- Never re-process an already-claimed unit. Key idempotency on stable identity like thread plus message id plus timestamp.
- Persist per-thread or per-unit cursors to the filesystem after each tick. A crashed loop resumes from the cursor, never from zero.
- Update a heartbeat file on every cycle. The heartbeat is the stall detector.
- Start every loop script with `set -e`. Failures propagate through exit codes, never silently.
- Run loops as shell steps under his cron jobs and supervisor scripts on the Linux VM via `cron.add` and `muse.exec`. Observe through heartbeat files and structured logs read at cycle boundaries, not through hooks.
- Separate plan, build, and judge into different agents with `subagent.spawn`. The judge must be independent of the builder, and its rules must be deterministic.

## Recover by Freezing, Shrinking, and Replaying

- Freeze the loop after repeated failures with the same root cause. Do not retry a failing loop unchanged.
- Reduce the scope of what the loop touches. Replay one unit against explicit acceptance criteria and confirm it passes.
- Resume full scope only after the replay passes and the human on call accepts the fix.
- Log the failure, the freeze, and the recovery in the structured log. Silence about a loop failure is itself a failure.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
