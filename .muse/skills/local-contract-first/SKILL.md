# Contract First Collaboration

Coordinate his Nova Workers backend and Flutter app through one authoritative contract. Use this whenever a backend-to-app boundary evolves on both sides and field drift is the risk.

## Record the boundary owners

- Name every consumer of the boundary before writing anything. For his work this is usually the Nova Flutter app talking to the Nova Workers backend, plus any agent or script that reads the same endpoints.

- Name the provider owner who implements the backend side. Name one approver who must sign off on contract changes, since one owner resolves ambiguity.

- Write down which artifact is authoritative: OpenAPI for HTTP APIs, JSON Schema for standalone payloads. Keep the artifact version-controlled in the repo, next to the code it governs.

- Reject duplicate sources of truth. Never maintain the same payload shape in a wiki page, a mock file, and provider code at the same time.

## Describe consumer jobs first

- Start from what each consumer must render or accomplish. Ask which fields the app actually reads before exposing anything.

- Define what missing, empty, and null mean for every nullable field. Document which identifiers must stay strings and which enum values the consumer can handle.

- Prefer one task-oriented response over several coupled calls when the consumer always needs the data together. Never expose a raw database row and call it a contract.

## Define the smallest useful contract

- Write the minimal schema that covers the consumer jobs, with required fields, nullability, enums, and error shapes explicit. Keep implementation details like database columns and query plans out of it.

```yaml
# contracts/order-summary.yaml
components:
  schemas:
    OrderSummary:
      type: object
      required: [id, status, total]
      properties:
        id:
          type: string
          description: Opaque identifier, never parse as a number.
        status:
          type: string
          enum: [pending, paid, cancelled]
        total:
          type: number
          minimum: 0
        cancellationReason:
          type: [string, "null"]
```

- Document semantic rules that syntax cannot express, such as which statuses allow a non-null cancellation reason. Add new fields only when a consumer job needs them.

## Verify both sides against the artifact

- Generate consumer types from the contract where the toolchain supports it, instead of handwriting copies. Build consumer mocks against the contract so app work continues while the provider is still in progress.

- Make the provider prove that real serialized responses satisfy the same artifact. Test success paths, each documented error, empty collections, nullable fields, and feature-flagged variants.

- Run at least one end-to-end happy path against the real provider before integration. Confirm no consumer reads undocumented fields.

- Review every generated diff with git diff before committing. Inspect type output, fixtures, and mocks for accidental changes.

```bash
npm run generate:api-types
git diff -- contracts/ generated/
```

## Change the contract before the code

- Never change implementation first and update the contract afterward. Propose the consumer need and its compatibility impact before touching code.

- Edit the canonical artifact and review the diff with affected consumers and the provider owner. Regenerate types, clients, and fixtures from the updated artifact.

- Update provider and consumer implementations to match the new contract. Run consumer and provider verification again after the change.

- Merge only when all affected sides agree on the new contract. Treat additive changes as compatible and verify old consumers still work.

- Route breaking changes through the versioning policy. Never silently repurpose an existing field to mean something new.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
