# API Connector Builder

Add a new provider, exchange, or API integration to one of his repos (for example a second exchange beside Kraken, or a new payment or notification provider) by matching the repo's existing integration pattern exactly. Use when wiring up one more integration without inventing a second architecture.

## Learn the house style

- Open at least two existing in-repo connectors with `muse.read` before writing any code. Map their file layout, abstraction boundaries, and config model.

- Record how each connector handles auth, retries, pagination, and errors. Note the registry or discovery wiring that makes the connector visible to the rest of the repo.

- Study the test style and fixtures alongside the implementation. Mirror the naming and assertion patterns exactly.

- Never invent a second integration architecture beside the repo's own. Start from the in-repo connectors, not from vendor docs.

- Prefer the newest current pattern when old connectors disagree. Never cargo-cult a deprecated layout.

## Narrow the integration surface

- Define only the surface the repo actually needs: the auth flow, the key entities, the core read and write operations, and the pagination and rate limits.

- Choose the webhook or polling model the repo already uses. Skip endpoints and features the repo will never call.

- Write the narrowed surface down before building. Build nothing speculative.

## Build in repo-native layers

- Build the connector in the repo's own slices: config and schema, client and transport, mapping layer, connector entrypoint, registration, and tests.

```
exchanges/
  kraken/
    config.py
    client.py
    mapper.py
    connector.py
tests/
  test_kraken_connector.py
```

- Validate config at load time and fail fast on missing credentials or bad values. Never hardcode secrets. Read them from the environment or the approved secret flow.

- Map provider payloads to the repo's internal models at one boundary. Keep provider-specific field names out of the rest of the codebase.

- Wire registration exactly like the existing connectors. The new connector must be discoverable the same way the old ones are.

## Validate against the source pattern

- Diff the new connector against an existing one file by file. Confirm the layout, naming, and layering match.

- Run the repo's test command with `muse.exec` and confirm the new tests pass alongside the old ones. Fix failures in the new code, never in the old tests.

- Confirm the new connector looks obvious in the codebase, not imported from a different ecosystem. A reviewer must not need the vendor docs to understand it.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
