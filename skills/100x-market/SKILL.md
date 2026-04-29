---
name: 100x-market
description: "Read public 100x market data such as states, depth, recent deals, klines, and market lists. Use for read-only market questions; no credential profile is required unless the user supplies one."
---

# 100x Market

Use this skill for public, read-only market data. These commands do not change
account state, so they can be run directly when the user's intent is clear.

## Flow

1. Identify the symbol, defaulting to no symbol only for list-style commands.
2. Run the narrowest read command.
3. Summarize the result in the user's units and mention the command used.
4. If data is stale, empty, or unavailable, report the CLI error and stop.

## Common Reads

Latest state for one market:

```bash
100x market state BTCUSDT
```

Order book:

```bash
100x market depth BTCUSDT
```

Recent public trades:

```bash
100x market deals BTCUSDT
```

Candles:

```bash
100x market kline BTCUSDT --interval 1m
```

Available markets:

```bash
100x market list
```

Use `--json` and `--jq` when calculating, filtering, or comparing:

```bash
100x --json market state BTCUSDT
100x --json --jq '.[] | select(.symbol == "BTCUSDT")' market list
```

## Boundaries

Do not use this skill for balances, positions, orders, triggers, or any account
state. Use `100x-account` for private reads and `100x-trade` for writes.
