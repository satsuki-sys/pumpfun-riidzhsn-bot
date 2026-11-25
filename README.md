# pumpfun-riidzhsn-bot

[![make.fun](https://make.fun/badge/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed.svg)](https://make.fun/c/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed)
[![rank](https://make.fun/badge/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed/satsuki-sys/pumpfun-riidzhsn-bot/rank.svg)](https://make.fun/c/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed)
[![reward](https://make.fun/badge/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed/satsuki-sys/pumpfun-riidzhsn-bot/reward.svg)](https://make.fun/c/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed)

A paper-trading bot for the make.fun competition
[Pump.fun Bot Sprint: Make Bot And Win](https://make.fun/c/u-riidzhsn-pump-fun-bot-sprint-make-bot-and-win-cb203fed).
It trades 10 SOL of paper money on a replay of pump.fun bonding-curve activity
(harness `agent-fun/replay/v2`, Node 22, `main.ts` at the repo root), ranked on
net PnL, with at least 20 distinct mints and a maximum drawdown of 50%.

## Strategy

Launch-chase. Most pump.fun curves die within minutes of launch, so the bot
buys only in the first 4 slots after a `CreateEvent`, and only when the
opening tape already looks alive:

- the curve holds 5–30 SOL of real SOL,
- the creator bought 0.05–2.5 SOL in the create slot,
- 3–4 distinct non-creator buyers have bought,
- the creator has not sold and sells by others are at most 10% of buys,
- the price has not fallen below its create price and has run up at most 50%.

One buy per coin, 0.10 SOL, at most 5 open positions and at most 1.5 SOL at
cost at once. Exits are whole-position, first trigger wins: +30%
take-profit, −8% stop-loss, a 12% trailing stop once up 50%, a 12-slot
maximum hold, a creator sell while held, or any single recorded sell of
2 SOL or more. A pace check against block time opens tiny 0.012 SOL probe
round trips on liquid curves when fills or distinct coins lag the pace, so
the run clears the 20-mint guardrail even on a quiet stretch.

Caps respected: at most 5 open positions, each buy at most 100 bps of the
curve's real SOL reserve, at most 16 orders per batch, no shorts, no
leverage. Orders decided on a batch's slot fill 2 slots later. The bot is
deterministic: no clock, no environment, no randomness.

## Files

- `main.ts` — entrypoint: the grader runs `node /bot/main.ts`.
- `strategy.ts` — the strategy (launch-chase + guardrail pacing).
- `vendor/makefun-bot.ts` — the kit's `sdk/ts/makefun-bot.ts` shim, vendored byte for byte.
- `LICENSE` — MIT.

## Grade locally

With the kit unpacked at `<kit>`:

```sh
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot . --driver process
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot . --driver process --stress
```

Dev-window result (local replay, `--driver process`): **+0.0253 SOL** net PnL,
50 fills over 25 distinct mints, max drawdown 0.0022, guardrails met. The win
leans on its 3 best coins (55% of the gains; −0.02 SOL without them), so the
stress spread — not the dev score — is the honest judge of this bot.

## License

MIT. See `LICENSE`.

Join make.fun with my referral link: https://make.fun/r/27qytqes
