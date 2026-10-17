# Onboard to a New Codebase

## When to use this

- Use this skill when you open a repository you have not worked in before, such as Nova, social-agent, or meme-agent.

- Use it when someone says "onboard me", "walk me through this repo", or "help me understand this codebase".

- Use it before writing code in a repo that was cloned mid-session.

## Run the four phases

- Run Phase 1 reconnaissance with parallel `muse.exec` calls: detect the manifest, fingerprint the framework, find entry points, snapshot the directory tree, detect tooling, and map the test layout.

- Skip reading every file and rely on search and listings instead, reading selectively only for ambiguous signals.

- Map the architecture in Phase 2: name the stack, the dominant pattern, and trace one request end to end from entry to response.

- Detect conventions in Phase 3 from real history: naming patterns, error handling style, and git conventions.

- Skip git-convention detection on depth-1 clones and note that history is unavailable.

- Flag anything you could not determine confidently instead of guessing it.

## Write the project note

- Write the onboarding note in his existing conventions, matching GOAL.md or AGENTS.md style.

- Keep the note scannable in two minutes and leave the details in the code.

- Extend an existing note rather than replacing it, and call out what you added.

- Stay read-only and report your findings until he asks for files to be written.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for cloning the repo and reading its recent commit history during convention detection.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
