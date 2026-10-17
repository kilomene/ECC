# JSON State Race Discipline

## Write state so concurrent writers cannot clobber it

- Keep every writer's state on disk in small files with one writer per file. Never let two loops rewrite the same JSON store in the same window.

- Create parent directories before writing. A journal write that runs before its directory exists fails and leaves the claim unlanded.

- Never run two watchers that write the same heartbeat file in parallel. Sequence them or give each its own file.

## Recover from phantom records

- When a tick returns an id that the store does not know, re-tick ONCE and use the new id. Do not spin on the dead id.

- When a claim reports success but the record still shows the old status, fail the stuck id once, re-tick once, then claim and fulfill immediately.

- When two tools disagree about one id, read the store file directly by id before acting. Never run a state-changing command against an ambiguous id.

## Key dedupe guards on stable identity

- Key every duplicate guard on a stable identity such as thread plus message id plus timestamp.

- Never fabricate a timestamp. A made-up timestamp creates a never-seen fingerprint and the guard misses the duplicate.

- Never feed synthetic placeholder text into a dedupe-guarded report. When the source has no verbatim text, omit the message instead of inventing it.

## Keep match patterns out of your own command line

- When grepping or killing by pattern, never let the literal pattern appear in your own invoking command line, or the tool matches your own shell.

- When building a kill list from pattern-matched PIDs, always exclude your own PID first.

- Put the pattern in a script file and run the file when the pattern cannot be kept out of the command line.

## Available connectors

- This skill uses native tools and does not require an account connector.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
