# Record Architecture Decisions

## When to use this

- Use this skill when choosing between significant alternatives such as frameworks, databases, patterns, or infrastructure.

- Use it when someone says "record this decision" or asks "why did we choose X".

- Read the existing ADRs first, before answering any question about past decisions.

## Read existing ADRs

- Check whether `docs/adr/` exists in the project.

- Scan `docs/adr/README.md` for entries that match the question.

- Present the Context and Decision sections of each matching ADR.

- Offer to record a new ADR when nothing matches.

## Draft the ADR

- Draft the ADR with these sections: Context, Decision, Alternatives Considered with a why-not for each, and Consequences including risks.

- Keep the whole document readable in two minutes.

- Record rejected alternatives explicitly so future readers see what was weighed.

- State trade-offs honestly, including what gets harder.

### Template

Follow this template when drafting:

```markdown
# ADR-NNNN: Decision Title

Date: YYYY-MM-DD
Status: proposed
Deciders: who was involved

## Context

2 to 5 sentences on the situation, constraints, and forces at play.

## Decision

1 to 3 sentences stating the decision clearly.

## Alternatives Considered

Alternative 1: name
Pros, cons, and why it was rejected.

Alternative 2: name
Pros, cons, and why it was rejected.

## Consequences

What gets easier, what gets harder, and the risks with mitigations.
```

## Get approval and file it

- Ask before creating `docs/adr/` in a repo for the first time.

- Present the draft and write the file only after explicit approval.

- Number new ADRs by scanning the existing directory and incrementing the highest number.

- Name the file `docs/adr/NNNN-title.md` and add the entry to the README index.

- Discard the draft without writing anything when the decision is declined.

- Keep durable decisions in the project's own `docs/adr/` and keep only a pointer in memory, for example that ADR-0007 records why a route was dropped.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
