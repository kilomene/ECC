# Attribution

## Upstream source

This harness derives procedures and checklists from
[affaan-m/ECC](https://github.com/affaan-m/ECC) (Copyright (c) 2026
Affaan Mustafa), used under the MIT License. The full original license
text is included in this repo as `LICENSE`.

## What was derived

The 28 skills in `.muse/skills/` marked `derived_from: "affaan-m/ECC (MIT)"`
in their `skill.yaml` were adapted from these ECC skills:

| This skill | ECC source |
|---|---|
| local-api-connector-builder | api-connector-builder |
| local-api-design | api-design |
| local-adr | architecture-decision-records |
| local-backend-patterns | backend-patterns (+ fastapi-patterns appendix) |
| local-browser-qa | browser-qa |
| local-change-impact | change-impact |
| local-codebase-onboarding | codebase-onboarding |
| local-config-gc | config-gc |
| local-contract-first | contract-first |
| local-d1-migrations | database-migrations |
| local-design-system | design-system |
| local-docs-lookup | documentation-lookup |
| local-e2e-testing | e2e-testing |
| local-error-handling | error-handling |
| local-eval-harness | eval-harness |
| local-flutter-code-review | flutter-dart-code-review (+ dart-flutter-patterns appendix) |
| local-gateguard | gateguard |
| local-git-workflow | git-workflow |
| local-github-ops | github-ops |
| local-production-audit | production-audit (+ canary-watch post-deploy appendix) |
| local-python-patterns | python-patterns |
| local-search-first | search-first |
| local-secure-backend-review | security-review |
| local-tdd-workflow | tdd-workflow (+ python-testing appendix) |
| local-trading-bot-security | llm-trading-agent-security |
| local-verification-loop | verification-loop |
| local-vuln-audit | security-bounty-hunter |
| local-watcher-loops | autonomous-loops (+ loop-design-check design section) |

How the adaptation was done: the concrete procedures, checklists, commands,
and factual patterns were transferred; all prose was rewritten for the Muse
runtime. Claude Code slash commands became file-based workflows, Claude hook
entry points became cron/watchdog patterns or manual checklists, and
stack-specific examples were rewritten for this operator's real stacks
(Cloudflare Workers/D1/R2, Flutter/Dart, Python services, plain HTML/JS
WebViews). Nothing was copied verbatim beyond short factual phrases.

Two triaged candidates were deliberately NOT shipped: `automation-audit-ops`
(marginal value over existing probe-then-conclude habits) and
`terminal-ops` (restated standing discipline without new procedure).

## Original work

These skills are original, written from the operator's own standing
workflows, and carry no upstream derivation:

- local-apk-build-discipline
- local-github-push-hygiene
- local-json-state-races

The `.muse/rules/` files and `.muse/agents/` brief templates are also
original.
