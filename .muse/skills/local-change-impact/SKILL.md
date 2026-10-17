# Change Impact Analysis

Predict the blast radius of a change before editing, then audit the finished diff against the declared scope. Read-only by default, and a guess is always marked as unverified.

## Predict the Blast Radius Before Editing

- Map the affected code through real imports and calls. Use `git grep` and `rg` to find every caller, consumer, and compatibility layer. Never infer relationships from directory names.
- Map the affected data. Name every schema, migration, serialization format, cache, and idempotency key the change touches.
- Map the affected interfaces. Name every API endpoint, message contract, file format, and CLI flag, plus who consumes and who provides each one.
- Name which tests prove the change correct, and which regression commands will run after it.
- Name the docs that must move with the change.
- Mark anything you cannot verify as unverified. Never present a guess as a fact.

## Plan the Release and the Rollback

- Write the deployment order and the compatibility window the change needs.
- Write the exact rollback command and the data recovery steps.
- Name every irreversible effect before the change starts. A missing rollback item is a blocker, not a detail to sort out later.

## Audit the Scope After the Work

- Compare the finished diff against the declared scope, line by line.
- Ask whether the diff exceeds what Osh asked for, whether the excess was authorized, and whether the artifacts match the actual change.
- Verify the literal contract: everything in his instruction is implemented, nothing is added, nothing is skipped, and nothing is "improved" beyond his words.
- Report scope drift and remaining risk in the delivery response. Unfinished migration work and temporary compatibility code get named as follow-ups, never left silent.

## Where This Skill Ends

- This skill predicts blast radius before the work and audits scope after it.
- The `delivery_boundary` skill declares what a finished delivery includes and excludes at announcement time. Use both together: change-impact guards the work, delivery_boundary guards the claim.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
