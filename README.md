# muse-ecc

Osh's personal agent harness for Muse (Meta's AI agent): a curated set of
31 custom skills, standing rules, and subagent brief templates, built by
surveying the [affaan-m/ECC](https://github.com/affaan-m/ECC) agent toolkit
and adapting everything worth keeping to the Muse runtime — plus original
skills drawn from real workflows on this machine.

## What it is

- `.muse/skills/` — 31 skills. Each is a folder with `SKILL.md`
  (instructions the agent follows) and `skill.yaml` (name, title,
  description, category). 28 are adapted from affaan-m/ECC (MIT, see
  `ATTRIBUTION.md`); 3 are original.
- `.muse/rules/` — standing rules: the non-negotiables every job runs under.
- `.muse/agents/` — brief templates. Muse has no slash-command agents;
  these are the exact briefs to hand to `subagent.spawn` for recurring roles.
- `.muse/examples/` — report and record templates the skills reference.

## Honest scope

- These are file-based skills for Muse's tool set (`muse.exec`,
  `muse.read`, `subagent.spawn`, `browser.search`, `cron`, and friends).
  There is no hook runtime and no slash-command system here; anything that
  was a Claude Code hook in the source material is rewritten as a
  cron/watchdog pattern or a manual checklist.
- Everything is $0. No skill requires a paid API, a subscription, or
  pay-as-you-go. Anything paid is disqualified by rule.
- Real procedures only. Every skill names the tools it uses and the exact
  shape of its output. Nothing is a placeholder.
- AI video production is intentionally NOT covered here — the separate
  Workflow skills (character-consistency, cinematic-effect-engine,
  physics-continuity-engine) are authoritative for that work.

## Install

Copy the skills into the live custom-skills directory:

```bash
cp -r .muse/skills/* ~/workspace/custom-skills/
```

Read `.muse/rules/` as standing context at the start of serious work.
Use `.muse/agents/*.md` as the `message` payload for `subagent.spawn`
when you need that role.

## Layout

```
.muse/
  skills/    31 skills (SKILL.md + skill.yaml each)
  rules/     standing rules (00-04)
  agents/    subagent brief templates (code-reviewer, qa-runner, researcher, release-manager)
  examples/  adr-template.md, qa-report-template.md, verification-report-template.md
ATTRIBUTION.md
LICENSE        MIT (affaan-m/ECC, Copyright (c) 2026 Affaan Mustafa)
README.md
```

## License and attribution

MIT for the upstream-derived portions — see `LICENSE` and
`ATTRIBUTION.md` for exactly what came from where.
