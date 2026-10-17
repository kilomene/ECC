# Python Patterns for His Scripts

## When to use this

- Use this skill when writing or reviewing any of his Python scripts: watcher loops, push scripts, supervisor scripts, and CLIs.

- Use it as a checklist before calling a Python change finished.

## Write clear typed functions

- Annotate every function signature with type hints in the modern style, such as `list[str]`, `dict[str, int]`, and `str | None`.

- Prefer EAFP over LBYL: catch the exception instead of checking conditions first.

- Keep modules small and focused, one job per file.

## Handle errors and resources cleanly

- Catch specific exceptions and raise informative errors with context, chaining with `from` to preserve the traceback.

- Return informative failures at system boundaries instead of returning `None` silently.

- Let no exception be swallowed silently. Log it or escalate it.

- Manage files, locks, and connections with context managers instead of manual cleanup.

- Use structured logging in loops instead of print debugging, and set log levels so normal runs stay quiet while failures stay loud.

## Follow his standing lessons

- Point TMPDIR and the pip cache at a workspace directory before heavy installs, because /tmp is a 512MB tmpfs that fills and kills big pip installs.

- Store no secrets in code. Read credentials from environment variables or the vault only.

- Store functions on test classes via `staticmethod` so instance access does not bind them to the test instance. A plain function assigned as a class attribute becomes a bound method of the test instance and poisons the module when restored in tearDown.

```python
# right: instance access returns the plain function
cls.orig = staticmethod(module.fn)

# wrong: self.orig becomes a bound method of the test instance
cls.orig = module.fn
```

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
