# Research Before You Code

## When to use this

- Use this skill before writing any utility, helper, or feature that might already exist.

- Use it when adding a dependency or an integration to a project.

- Use it when a task starts as "add X functionality" and writing code is the default plan.

## Scope the need

- Write one short paragraph that states exactly what functionality is required.

- List the language and framework constraints before running any search.

- Define what counts as a match so you know when to stop searching.

## Sweep the channels in parallel

- Search his own repos first with `rg` for a prior implementation to reuse.

- Query the package registries for npm and pip equivalents of the need.

- Run GitHub code search through the `gh` CLI for maintained open source implementations.

- Run `browser.search` for project names and alternatives the registries did not surface.

- Launch a parallel research sweep with `subagent.spawn` for non-trivial needs, one worker per channel.

- Report honestly which channels you searched and which were unavailable.

## Evaluate and decide

- Score each candidate on functionality, maintenance health, license, and dependency weight.

- Apply his zero budget gate: accept only MIT or Apache licensed code, or a genuinely free tier with no card and no pay-as-you-go billing.

- Disqualify anything paid outright, no matter how good the fit looks.

- Read the license file and the pricing page instead of trusting README claims.

- Adopt the candidate as-is when it matches exactly and is well maintained.

- Extend with a thin wrapper when the match is partial but the foundation is solid.

- Build custom only when nothing close exists, informed by what the research taught you.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for searching repos, code, and release health before adopting a project.

- [Zapier](/opt/hatch/connectors/zapier/CONNECTOR.md) for checking whether an integration already exists as an automation instead of new code.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
