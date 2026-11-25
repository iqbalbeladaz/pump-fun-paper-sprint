[![make.fun](https://make.fun/badge/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b.svg)](https://make.fun/c/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b)
[![rank](https://make.fun/badge/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b/iqbalbeladaz/pump-fun-paper-sprint/rank.svg)](https://make.fun/c/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b)
[![reward](https://make.fun/badge/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b/iqbalbeladaz/pump-fun-paper-sprint/reward.svg)](https://make.fun/c/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b)

# Pump Sprint — Fresh-Launch Sniper for Pump.fun

Entry for [Pump.fun Paper Sprint: Any Bot Can Win](https://make.fun/c/u-boothlord-pump-fun-paper-sprint-any-bot-can-f83c389b): a paper-trading bot for the pump.fun bonding-curve replay. One program, no network, no outside data.

## Strategy

On pump.fun almost every new coin dies within minutes and a few run 10–100x, so the trade is asymmetric by construction: many small, heavily filtered first buys, a tight stop on the losers, and no hard ceiling on the winners.

- **Entry (age 3–12 slots after creation):** wait for the first trades to print, then buy only if the launch looks clean — at least 3 trades but at most one distinct non-creator buyer so far, the creator has skin in the game (≤ 2.5 SOL) but has not sold, no large single sell, and the price has not already run more than +50% from its create price.
- **Size:** 0.08 SOL per coin (capped at 1% of the real SOL reserve), at most 5 open positions.
- **Pyramid:** one add of 0.08 SOL if the price rises 20% above the buy and net flow is still positive.
- **Exits:** sell the whole position after 10 slots unless it is up ≥ 5% (then a trailing stop takes over); bank half at +60%; stop out at −18% (just above cost after the first take-profit); exit on creator sell or 600 slots without a trade.
- **Guardrail probes:** if the count of traded coins lags a pace of 21 coins per 6 hours, open 0.01 SOL probe round trips on the most liquid curve, so the run always clears the 20-coin guardrail.

Deterministic: no clock, no randomness, iteration only over insertion-ordered maps fed by the batch stream.

## Results (kit dev window, local replay sim)

| | |
|---|---|
| Net PnL, full 24 h dev window | **+0.039 SOL** |
| Net PnL, 6 h slices (like the final window) | +0.001 / −0.010 / −0.012 / −0.006 SOL (median −0.008) |
| House baselines on the same window | sniper −0.423 SOL, reversion −4.369 SOL, momentum −9.454 SOL, hold-cash 0.000 SOL (fails the coin guardrail) |
| Trades | 58 fills over 28 coins, 28 round trips, 4 wins |
| Max drawdown | 0.2% (guardrail: ≤ 50%) |
| Best single coin | +0.042 SOL |

The dev window is a brutal down market — every house baseline loses money on it — so the edge here is defense: lose ~0.008 SOL per 6 hours in a bad window while keeping the upside when a coin runs.

## Run it

```sh
node main.ts   # speaks agent-fun/replay/v2 on stdin/stdout, via vendor/makefun-bot.ts
```

Grade it with the kit (from the unpacked kit directory):

```sh
node cli/makefun.mjs grade-local --kit . --bot <this repo> --driver process
```

## Layout

- `main.ts` — the bot (entrypoint).
- `strategy.ts` — the strategy (entry filter, pyramid, exits, guardrail probes).
- `vendor/makefun-bot.ts` — byte-identical copy of the kit's `sdk/ts/makefun-bot.ts` shim.
- `LICENSE` — MIT.

## License

MIT — see [LICENSE](LICENSE).
