# Garbage Collect Your Config

## When to use this

- Use this skill after big skill installations and on a roughly 30-day review.

- Use it when he says "clean up my config", "audit my setup", or complains about bloat.

- Apply it to one channel at a time or the full set: custom skills, memory files, cron jobs, tracked items, and workspace caches.

## Scan each channel

- Scan `~/workspace/custom-skills` for overlapping names, broken SKILL.md files, and domains that do not match his actual work.

- Scan `~/memory` for duplicate index entries, entries that contradict newer ones, dates that have passed, and orphan files missing from the index.

- List cron jobs with `cron.list` and flag fired one-shots older than 30 days plus jobs whose target scripts no longer exist.

- Review tracking items for completed work left open and for items that newer ones replaced.

- Sort workspace caches and tmp dirs by size and mtime with `muse.exec`, and flag large items older than 30 days.

- Rank every candidate by confidence and present it with the evidence that flagged it.

## Confirm one by one

- Present each candidate with its evidence and ask for a yes or no before touching it.

- Approve items individually and reject bulk approval for the whole list.

- Treat age as a signal, not a verdict. A quiet item may be seasonal.

- Stop immediately when he says stop.

## Soft-delete and log

- Soft-delete first: rename to `.disabled` for skills, or move files into a trash directory.

- Run the hard delete only on a later pass, and only for items he confirms again.

- Append every run to a GC log with the timestamp, what was touched, why, and the undo instructions.

- Keep the log forever. Questions like "when did I disable that and why" come up more than you expect.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
