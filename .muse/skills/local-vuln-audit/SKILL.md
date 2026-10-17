# Vulnerability Audit

Audit Osh's own public backends (the Nova API, Cloudflare Workers, Python services) for real exploitable flaws before release. Claim nothing you cannot prove.

## Enumerate real entrypoints

- List every place the outside world reaches the code: HTTP handlers, file uploads, webhooks, parsers, and integration endpoints.
- Read the program scope first: what deployments are covered and what is explicitly out of scope. Audit only what is in scope.
- Ignore local-only helpers, CLI-only tooling, demo code, and paths no network user can reach.

## Triage with static tooling

- Run `semgrep --config=auto --severity=ERROR --severity=WARNING` with `muse.exec` over the repo. Treat the output as triage input only, never as a finding list.
- Drop every finding that lives in tests, demos, fixtures, examples, or vendored code. Drop anything with no user-controlled route to it.
- Keep only findings where an attacker can plausibly reach the code from a real user or network boundary.

## Prove user control reaches the sink

- Read the full code path end to end, from entrypoint to the risky call. Do not trust the scanner's file and line alone.
- Trace the input value through every function call. Prove the attacker controls the value that lands in the sink.
- Check for sanitization, allowlists, or auth gates along the way. A gate you missed turns a finding into a false positive.
- Focus on these patterns: SSRF through user-controlled URLs, auth bypass in middleware or guards, deserialization paths that lead to code execution, SQL injection in reachable endpoints, command injection in request handlers, path traversal in file-serving paths, and stored or reflected XSS.

## Write a safe proof of concept

- Build the smallest proof of concept that demonstrates the flaw without damaging data. Read-only impact is enough to confirm exploitability.
- Test against a local copy or a staging deploy first. Never run an exploit proof against the live production backend with destructive payloads.
- Confirm the PoC actually works. An untested theory is not a finding.
- Confirm the issue is not already covered by a CVE, advisory, or open fix ticket before escalating it.

## Record and disclose findings

- Log every confirmed finding as a fix task in `tracking` with the file, line, pattern, and impact. Work the fixes before the release ships.
- If a flaw lives in a third-party dependency rather than his code, follow responsible disclosure: report it to the maintainer through their published channel instead of publishing the details yourself.
- Report to Osh with the file and line for each finding and what you proved with the PoC. Anything unproven goes back to triage, not to the findings list.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for checking existing advisories, CVEs, and open fix tickets against his repos.
- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
