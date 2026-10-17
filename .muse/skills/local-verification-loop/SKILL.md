# Verification Loop

## Run the six phases in fixed order

- Run every command through `muse.exec` with `set -o pipefail` at the top of the script. Piped commands must report the real exit status and never hide a failure behind tail or sed.

### Build

- Build the project with its own build command, such as `npm run build`, `flutter build`, or `cargo build`. Record the exit status and the last lines of output.

- Treat any build failure as a hard stop. Fix it before phase two.

### Typecheck

- Typecheck TypeScript with `tsc --noEmit`, Dart with `flutter analyze`, Python with pyright or mypy where the project uses one, and Rust with `cargo check`. Show the first errors and fix the critical ones.

- Keep typecheck failures in the report even after you fix them. They show what the phase caught.

### Lint

- Run the project linter: `npm run lint` for JavaScript or TypeScript, `ruff check .` for Python, `cargo clippy` for Rust, `flutter analyze` for Dart. Limit displayed output with sed while consuming the full stream so pipefail stays honest.

- Hold zero warnings as the bar. Record every warning you leave in place and the reason.

### Tests with coverage

- Run the full suite with coverage enabled and require 80 percent or higher. Report totals: tests run, passed, failed, and the coverage percentage.

- Treat a failing test as a hard stop. Fix the code or fix the test, never delete the test to make the phase pass.

### Security grep

- Search the diff for hardcoded secrets, API keys, tokens, and passwords. Search for stray `console.log` or debug print calls left in source.

- Treat any secret in the diff as a hard stop. Remove it, rotate it if it ever left the machine, and note the rotation in the report.

### Diff review

- Read `git diff --stat` and walk every changed file. Flag unintended changes, missing error handling, and untested edge cases.

- Confirm the diff holds only what the task intended. Revert the rest or move it into its own change.

## Treat the first failure as a full stop

- Halt the loop the moment a phase fails and fix the failure before you continue. A later passing phase never cancels an earlier failing one.

- Rerun from the failed phase after every fix. Rerun the whole loop when the fix touched shared code, since a fix in one phase can break another.

## Deliver the PASS/FAIL report

- End every run with a written verification report, one line per phase with PASS or FAIL and the counts behind it. Close with a single READY or NOT READY call.

- List every issue to fix in priority order. Name the file and the exact failure for each.

```
VERIFICATION REPORT

Build:     PASS
Types:     PASS (0 errors)
Lint:      PASS (0 warnings)
Tests:     PASS (142/142, 87% coverage)
Security:  PASS (0 issues)
Diff:      6 files changed

Overall:   READY

Issues to Fix:
1. ...
```

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for the PRs this gate protects and the diffs it reviews.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
