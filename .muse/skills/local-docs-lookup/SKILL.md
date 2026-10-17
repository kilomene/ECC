# Look Up the Official Docs First

## When to use this

- Use this skill for any question about a library, framework, or API.

- Use it for setup questions, API references, and code that depends on library behavior.

- Use it whenever an API answer is not already backed by verified docs.

## Resolve the identity and version

- Pin down the exact library name and the version the project actually uses.

- Read the project's lockfile or manifest for the real version instead of recalling one.

- Treat an unconfirmed version as unknown and say so in your answer.

## Fetch the authoritative docs

- Open the official documentation first with `browser.open` on the known doc URL.

- Constrain follow-up lookups with `browser.search` using a `site:` query on the project domain.

- Start `browser.search` at site:developers.cloudflare.com for Workers, D1, R2, and the other Cloudflare products.

- Use the official Flutter and Dart docs for Flutter questions and the official Python library reference for stdlib questions.

- Use the official Android developer docs for ADB questions.

- Cap yourself at 3 lookups per question and stop there.

- Strip secrets, keys, and tokens from any query string before you send it.

## Answer from the docs

- Base your answer on what the fetched docs say, not on what you assumed.

- Prefer the official docs over community forks when sources conflict.

- Cite the version alongside every code example you give.

- State your uncertainty plainly when the docs did not settle the question, instead of guessing.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
