# GitHub Push Hygiene

## Point at the repo root, never a subdir

- Pass the directory whose tree IS the full repo as the local source. Never pass a subdirectory.

- Always use an absolute path for the local directory. A relative path combined with a `cd` in the same command resolves somewhere you did not intend.

## Arm the abort guards

- Abort the push when the local directory does not exist.

- Abort when the plan deletes far more than it uploads. Deleting more than 10 files while uploading fewer is a mass-delete signature, not a cleanup.

- Never hide the plan summary behind a pipe to `tail`. Read the full "files to upload, files to delete" counts before anything runs.

## Verify against the remote tree

- After the push, fetch the remote recursive tree and diff it against the local walk. Zero missing and zero extra is the pass condition.

- A commit status alone does not prove the tree is right. Diff the trees.

## Recover from a bad push

- When a push deletes files it should not have, move the branch ref back to the pre-push SHA with a forced ref update through the GitHub git-refs API. One call restores the branch.

- Re-upload the intended files at their correct repo paths afterward, then diff the trees again.

## Keep pushes small and honest

- Test on this machine and watch it work before GitHub ever sees it. Push once when green.

- Prefer put-only recovery scripts for restoring files. A script with no delete path cannot mass-delete.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for repo, ref, and release operations backing these procedures.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
