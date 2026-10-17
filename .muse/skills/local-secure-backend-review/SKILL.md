# Secure Backend Review

Run this FAIL/PASS audit on every backend before any public push. One FAIL blocks the push until fixed.

## Scan for leaked secrets

- Run the scan with `muse.exec` using ripgrep against the full repo tree: `rg -n --no-ignore-dot -i -e 'sk-(live|proj)-[A-Za-z0-9]' -e 'aws_secret|ghp_[A-Za-z0-9]|xox[bap]-' -e '(api[_-]?key|password|passwd|secret|token)\s*[=:]' -- . | rg -v '(process\.env|os\.environ|\bexample\b|placeholder)'`
- Read every hit with `muse.read` before judging it. A match in docs or a placeholder string is not a leak. A real value in code, config, or history is a FAIL.
- Store secrets in environment variables only. Keep `.env` files in `.gitignore` so they never reach git.
- On Cloudflare Workers, store production secrets with `wrangler secret put <NAME>`. Read them at runtime with `env.<NAME>` instead of hardcoding.
- Keep agent credentials (logins, tokens, API keys you use) in the Secure Vault, never in files, chat logs, or memory notes.
- Validate at startup that every required variable exists and exit loudly when one is missing. Use a Python pattern like `os.environ["REQUIRED_KEY"]` that raises on absence instead of a `.get()` that silently returns None.

## Validate every input

- Validate user input at every system boundary with a schema, not with inline if checks. Whitelist allowed shapes instead of blacklisting bad ones.
- For Python services use a schema library such as Pydantic or a `zod`-style schema for TypeScript code. Reject anything that does not parse and return a 400 with a generic message.
- Validate file uploads by size, MIME type, and extension together. Reject anything over the documented limit before reading the full payload.
- Never place user input inside a SQL string. Pass every value through your driver or ORM as a bound parameter.

## Enforce auth on every route

- Place an auth check on every route, not just the obvious ones. Health endpoints and admin panels count.
- Deny access by default and require an explicit allow. A route without an auth check is a FAIL.
- Verify the caller is allowed to act on the specific resource they request, not just that they are logged in. Ownership checks belong before the operation, not after.
- Keep session tokens in httpOnly cookies with Secure and SameSite set. Never store tokens where script can read them.

## Harden public endpoints

- Rate limit every public endpoint. Give expensive operations (search, generation, file handling) stricter limits than simple reads.
- Return generic error messages to callers. Log the detailed error server side only. Never expose stack traces or internal paths in responses.
- Redact secrets, tokens, and full card numbers from logs. Log user IDs and last-four digits instead.
- Encode output before rendering it in HTML or injecting it into the DOM. Sanitize any user-provided HTML with an allowlist of safe tags.

## Write the PASS/FAIL report

- End the audit with a written report to Osh. Open with one line stating PASS or FAIL for the repo.
- Name every failing check with its file path and line number, one bullet per finding.
- Fix each FAIL with `muse.edit` and re-run the scan before reporting PASS. A fixed finding stays in the report as resolved.
- Push only when every check passes. His standing rule says auth on every route and a secret scan before every public push. This audit enforces both.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for secret-scanning alerts and Dependabot updates on the pushed repo.
- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
