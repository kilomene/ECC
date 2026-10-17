# Eval Harness for Loops and Bots

## When to use this

- Use this skill before changing code in his agent loops and trading bots.

- Use it to define what success looks like before implementation, not after.

- Use it when a fix needs proof that the old behavior still works.

## Define evals before coding

- Write capability evals that state the exact behavior the loop or bot must show.

- Write regression evals that capture the behaviors the change must not break.

- Record each eval as a checklist with clear pass criteria.

- Version the evals alongside the code they test.

## Grade and measure

- Run deterministic code checks with `muse.exec` wherever behavior is scriptable.

- Grade open-ended output with a model-graded rubric in a second pass.

- Flag judgment calls for human review with the risk level attached.

- Track pass@k as at least one success in k runs, and pass^k as k consecutive successes.

- Measure behavior, never spend, and keep cost and token accounting out of eval reports.

## Apply to his loops and bots

- Test kill-switch behavior with a deterministic eval that must pass every run.

- Test paper-trade decisions against recorded baselines before changing strategy code.

- Test watcher dedupe behavior by replaying known duplicate scenarios.

- Run the full eval set after every fix and record the results next to the change.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
