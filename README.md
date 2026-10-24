<p align="center">
  <img src="/paperjev.png" alt="PaperJev" width="100%">
</p>

# PPJ PaperJev

**An automated trader that runs on Jev and trades on [Papertrade](https://papertrade.xyz).**

A TypeSafe Jev model watches Hyperliquid's BTC and ETH order books and makes one call per tick: long, short or flat. PaperJev turns that call into positions on Papertrade, a fully on-chain synthetic perpetuals exchange on HyperEVM. Every open and close fills at Hyperliquid's BBO mid, against Papertrade's LP. There is no order book, no slippage, no spread and no funding.

A small server streams every tick, position and settlement to the dashboard.

## Bot wallet

PaperJev's automated trading wallet:

```
8wgvWJTDqgR7prueMRZuXR22xuBmtGRN72S3EzTpVf5x
```

> **Real money.** Papertrade is a live exchange with up to 1000× leverage, and the "paper" is only the name. With a session key configured, PaperJev trades your real USDC balance. Start in dry-run mode and keep leverage low.

## Quick start

```bash
cp .env.example .env
bun install
bun run start
```

With no `SESSION_KEY`, PaperJev dry-runs. It uses real Hyperliquid prices and real model decisions, then simulates opens and closes at BBO mid. Because Papertrade itself fills at BBO mid, dry-run entry and exit prices match what the exchange would have given you, apart from relayer timing.

Set `MODEL=jev` and `TYPESAFE_AI_API_KEY` to trade with Jev. The default `mock` model is a momentum heuristic for testing the pipeline.

## How Papertrade trades

These mechanics shape the whole strategy, so they're worth knowing before you run it. They're from the Papertrade docs at the time of writing.

- **Counterparty.** Every trade is a synthetic swap with the LP. Nothing is traded on Hyperliquid itself.
- **Price.** Opens and closes execute at Hyperliquid's BBO mid, read on-chain through a HyperCore precompile, whatever the size.
- **Markets.** BTC and ETH, up to 1000× leverage, and up to $10M notional per position.
- **Positions.** Each position is self-contained, with its margin fixed at open. There's no top-up, no partial close and no leverage change. To change size, close the position and open a new one.
- **Costs.** There's no gas for the trader, no funding and no spread. Profitable closes pay an asymmetric-impact haircut plus a 2% win fee, and a 0.5% builder fee if a builder is named. Losses pay nothing extra.
- **Execution path.** You sign intents with a session key. Papertrade's relayer batches them through `BatchExecutor` into `Exchange`. Opens and closes can't be sent to the contracts directly.
- **Session keys.** A session key can open and close positions but can't withdraw. It lasts 30 days, and each signed intent is valid for one hour. You can revoke it from your wallet with `revokeSessionKey`.

Because Papertrade charges on winning closes, flipping every block would bleed fees. PaperJev holds positions and only trades when the model's view changes with enough conviction.

## How a tick works

1. **Read.** PaperJev reads the Hyperliquid BBO and recent trades for each enabled market.
2. **Decide.** Jev is asked about the move over `HORIZON_SECONDS` and returns long, short or flat probabilities.
3. **Act.**
   - If the model agrees with the current position, PaperJev holds.
   - If it flips with conviction above `MIN_CONVICTION`, PaperJev closes the position and opens one in the new direction.
   - If it calls flat, or conviction is too low, PaperJev closes or stays out.
   - Opens use `MARGIN_USD` at `LEVERAGE`.
4. **Sign and send.** The intent is signed with the session key and posted to the Papertrade relayer. Settlement comes back on a later tick as its own event.
5. **Account.** PaperJev reconciles balance, open positions, realized P&L, fees and Jev cost against on-chain `Exchange` state.

If the model misses its budget, nothing is sent and the tick records `late: true`.

## API

| Endpoint | Description |
| --- | --- |
| `GET /` | Snapshot: model, account, open positions, latest tick |
| `GET /history` | Last 1000 tick events |
| `GET /events` | Server-sent events: snapshot on connect, a `tick` event per decision, an `intent` event when an order is sent, a `settle` event when it lands, and a `ping` every 15 s |

### Tick event

```json
{
  "ts": 1792875930676,
  "market": "BTC",
  "mid": 112345.5,
  "bestBid": 112345.0,
  "bestAsk": 112346.0,
  "decision": {
    "action": "long",
    "probabilities": { "long": 0.71, "short": 0.22, "flat": 0.07 },
    "latencyMs": 84,
    "late": false
  },
  "intent": { "kind": "open", "side": "long", "marginUsd": 10, "leverage": 20, "status": "sent", "id": "pj-12" },
  "position": { "side": "long", "notionalUsd": 200, "entryPrice": 112340.0, "bustPrice": 106723.0, "unrealizedUsd": 0.01 },
  "totals": { "ticks": 412, "opens": 9, "closes": 8, "wins": 5, "losses": 3, "lateTicks": 0, "feesUsd": 0.42, "jevUsd": 0.0006, "realizedUsd": 1.87, "pnlUsd": 1.88 }
}
```

### Settle event

```
event: settle
data: { "id": "pj-12", "kind": "open", "status": "filled", "price": 112340.0, "txHash": "0x…" }
```

`status` is `filled`, `rejected` or `expired`, the last meaning the intent wasn't included within its one-hour validity. `txHash` is the relayer's batch transaction on HyperEVM. In dry-run mode the status is `sim`, there's no `txHash`, and the fill price is the BBO mid at send time.

## Configuration

| Variable | Default | Purpose |
| --- | --- | --- |
| `MODEL` | `mock` | `jev` or `mock` |
| `TYPESAFE_AI_API_KEY` | — | Required for `MODEL=jev` |
| `MARKETS` | `BTC` | Comma-separated: `BTC`, `ETH` |
| `SESSION_KEY` | — | Papertrade session key. Without it, PaperJev dry-runs |
| `ACCOUNT` | — | Your wallet address, used for balance and position reads |
| `RELAYER_URL` | — | Papertrade relayer endpoint for signed intents |
| `HYPEREVM_RPC_URL` | public RPC | Reads of `Exchange` state and settlements |
| `MARGIN_USD` | `10` | Margin per position |
| `LEVERAGE` | `10` | Leverage per position, 1000× max on Papertrade |
| `HORIZON_SECONDS` | `30` | How far ahead the model is asked to predict |
| `MIN_CONVICTION` | `0.65` | Probability needed to open or flip |
| `TICK_MS` | `1000` | How often to ask the model |
| `MAX_DAILY_LOSS_USD` | — | Stop trading for the day after this realized loss |

## Project layout

```
src/config.ts       environment
src/feed.ts         Hyperliquid BBO + trades feed (WebSocket, polling backstop)
src/model.ts        Model interface, JevModel (AI SDK experimental_evaluate), MockModel
src/papertrade.ts   intent building and signing, relayer client, Exchange reads, settlement tracking
src/sim.ts          dry-run executor: fills at BBO mid, applies Papertrade's fee model
src/trader.ts       the loop: one decision in flight, conviction gating, risk limits, P&L accounting
src/server.ts       Bun.serve: snapshot, history, SSE
```

## Risk notes

- **Leverage.** At high leverage the bust price sits very close to entry. At 1000×, a 0.1% move against you liquidates the position.
- **Fees on wins.** The win fee and impact haircut mean a strategy's gross edge has to clear them on every winning close.
- **LP solvency.** If Papertrade's LP is insolvent, winnings are paid from a payout queue as other users' losses come in, not immediately.
- **Session key.** A leaked session key can't withdraw your funds, but it can trade them away. Keep it out of the repo and revoke it if in doubt.

Read the [Papertrade docs](https://docs.papertrade.xyz), especially the risk, liquidation and payout-queue pages, before going live.

## Credits

Based on [jev-trader](https://github.com/jarrodwatts/jev-trader) by Jarrod Watts and contributors.

## License

MIT
