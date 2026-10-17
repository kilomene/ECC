# Git Workflow

Work in Git the way Osh requires: branch per change, conventional commits, proved on his own machine before GitHub ever sees it, and released as tagged builds with signed APKs.

## Commit with Conventional Grammar

- Write every commit message as `<type>(<scope>): <subject>`. Keep the subject in imperative mood, under 50 characters, with no trailing period.
- `feat` marks a new feature. `fix` marks a bug fix.
- `docs` marks documentation changes. `style` marks formatting with no code change.
- `refactor` marks code restructuring with no behavior change. `test` marks test additions or updates.
- `chore` marks maintenance work. `perf` marks a performance improvement.
- `ci` marks CI and pipeline changes. `revert` marks a reverted commit.
- Put one change on one branch. Name it `feature/<name>`, `fix/<name>`, or `hotfix/<name>`. Keep `main` always releasable.
- Follow simple GitHub-flow: branch from `main`, open a PR, merge after review and green CI, deploy right after the merge.

## Prove It on Your Own Machine First

- Build and run everything on the Linux VM before pushing. Watch it work with your own eyes. GitHub never sees untested code.
- Never stack CI runs. Cancel every queued or pending run, then fire ONE fresh run.
- Fix the root cause on the spot. Retest. Push once when green. CI is not a debugger.
- Use `muse.exec` for every git and `gh` command.
- If a push goes wrong, recover by force-moving the branch ref to the pre-push SHA with the GitHub git-refs API. Send `PATCH /repos/{owner}/{repo}/git/refs/heads/<branch>` with `{"sha": "<pre-push-sha>", "force": true}`. Verify the result by fetching the remote tree and comparing it file by file against the local tree.

## Cut Tagged Releases with Signed APKs

- Release only from a green `main`. Tag as `vMAJOR.MINOR.PATCH` with MAJOR for breaking changes, MINOR for backward-compatible features, and PATCH for fixes.
- Attach the release APK built and verified locally. Never attach a binary you have not installed and run.
- Create the release with `gh release create v1.2.0 --title "v1.2.0" <apk-file>`. Let the release notes summarize what changed.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for repo, branch, and release operations that go beyond local git commands.
- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
