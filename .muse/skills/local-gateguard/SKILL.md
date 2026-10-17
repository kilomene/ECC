# GateGuard Fact Check

Gather facts before the first write to any file. This checklist fixes the "guess instead of investigate" failure class, and it matters most on his trading bots and backend code.

## Run the Checklist Before the First Write

- List every file that imports or calls the code you are about to touch. Search the whole tree with `rg` or `git grep`.
- Name the public surface the change affects: exports, endpoints, CLI flags, and configuration keys.
- For code that touches data, record the exact schemas, field names, and date formats. Use redacted or synthetic values, never raw production data.
- Quote Osh's instruction for this change verbatim.
- Run this checklist as a plain manual pass, not as a hook. No hook runtime exists here.
- When the change touches no public surface, ask instead which call sites inside the file or its module rely on the changed behavior.

## Ask Change-Adaptive Questions

- If the public surface moves, name every consumer that must move with it. No consumer left unnamed means the surface did not move.
- If a data shape changes, name the migration path. Schema changes without a migration path do not ship.
- If you are unsure whether the surface moves, treat it as moving until you prove otherwise.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
