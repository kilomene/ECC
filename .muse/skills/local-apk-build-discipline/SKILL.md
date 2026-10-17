# APK Build Discipline

## Announce the build path first

- Say where the build is happening at the start of every build, in plain words like "building locally now".

- Build on this machine, install the APK, launch it, and watch it work before GitHub ever sees it.

- Never present a finished build that has not run on a real machine. A build that only compiled is not verified.

## Test every corner

- Test what was fixed, then re-test after each new fix until everything works. Fix properly and never patch and move on.

- Exercise the exact path the user will take: tap through the screens, run the flows, install and launch the build the way he will.

- For UI work, show rendered samples directly in chat. Links alone are not enough.

## Sign and release cleanly

- Sign every release APK and verify the signature before attaching it to a release.

- Uninstall the old APK from the test device before installing a cross-release upgrade built with a fresh key, or the install fails on signature mismatch.

- Ship releases as tagged GitHub releases carrying the signed APK as the asset.

## Keep CI honest

- Never stack CI runs. Cancel queued and pending runs, then fire ONE fresh run.

- When a run fails, read the failure log and fix the root cause on the spot. Retest locally, then push once when green.

- CI is not a debugger. Fix on this machine first, always.

## Available connectors

- [GitHub](https://github.com) via the `gh` CLI through `muse.exec` for releases and run triage. Use the github connector doc for API-driven repo work.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Alternatives for better results

- Pair with local-verification-loop when the change touches code: run its six-phase gate before the install-and-watch step here.
