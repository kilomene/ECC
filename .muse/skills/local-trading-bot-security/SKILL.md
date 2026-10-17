# Trading Bot Security

Protect the trading bots (Lever on Kraken spot-margin, meme-agent) with layered defenses. No single check is enough. The layers compose: injection guard, spend limits, pre-flight validation, circuit breaker, key isolation, kill switch.

## Scan inputs for prompt injection

- Treat every external text source as hostile before it reaches an execution-capable prompt: market feeds, social chatter, webhook payloads, token names, and pair labels.
- Scan incoming text for injection patterns with a matcher before the bot acts on it. Reject or quarantine the payload when a pattern hits.
- Never place raw external text inside a prompt that can trigger orders. Label injected data as data, never as instructions.

## Enforce spend limits

- Cap the risk per trade at 1 percent of the account. Refuse any order that exceeds it.
- Cap the number of open positions at 2. Refuse a new order while 2 positions are open.
- Cap the daily loss at minus 3 percent of the account. Halt all trading for the rest of the day when the cap is hit.
- Enforce these limits in code, independent of the bot's model output. A limit that depends on the model's goodwill is not a limit.

## Validate orders before sending

- Dry-run every order against the exchange before sending the real one. Use the exchange's test or paper path where available.
- Check slippage against the quoted price before confirming. Reject the order when the slippage exceeds the per-strategy threshold.
- Verify balance, position size, and price limits on every order. Log the validated order parameters before sending.

## Build the circuit breaker

- Track consecutive losses. Halt trading after 3 consecutive losses and require a manual reset before resuming.
- Track daily PnL. Halt trading when the daily loss reaches minus 3 percent.
- Halt on invalid state too: missing price data, stale balances, or a failed exchange connection stop the bot instead of letting it trade blind.
- Make the halt fail closed: a halted bot sends nothing and requires an explicit restart from Osh or the supervisor.

## Isolate wallets and keys

- Read every key from environment variables or encrypted deploy secrets at startup. Fail closed when a required key is missing.
- Never store keys in git, in files on disk, in chat logs, or in memory notes. Rotate any key that leaks and treat the leak as an incident.
- Give each bot a dedicated wallet holding only its session funds. Never point a bot at the primary treasury wallet.
- Keep the supervisor checking a file-based kill switch. When the file exists or is set to stopped, the bot halts immediately and stays halted until the file is cleared.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for keeping the bots' encrypted deploy secrets out of the repos they deploy.
- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
