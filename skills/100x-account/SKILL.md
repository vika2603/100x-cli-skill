---
name: 100x-account
description: "Read and explain private 100x account state with the CLI: balances, margin, exposure, positions, open risk, pending orders, triggers, fills, finished orders, preferences, and position history. Use for read-only account health checks and investigations."
---

# 100x Account

Use this skill for private read-only account inspection. The goal is to answer what
is happening in the user's account, not to dump every command. These commands require
a configured profile, but they do not place or modify orders.

## Flow

1. Determine the profile. If the user named one, run commands with
   `--profile <profile>` and tell the user which profile is in use. If the user
   did not name one, run commands without `--profile` (the CLI uses its current
   default) and tell the user which profile that resolved to. The examples below
   show the explicit `--profile <profile>` form for clarity.
2. Classify the request as overview, exposure/risk, pending activity, or history.
3. Run only the reads needed for that path.
4. Summarize in account terms: available balance, margin in use, unrealized PnL,
   open positions, pending orders, active triggers, and recent finished activity.
5. If the user asks to place, edit, cancel, close, change margin, or change risk,
   switch to `100x-trade`.

## Account Overview

For "how is my account", "account status", or "risk overview", start with:

```bash
100x --profile <profile> futures balance list
100x --profile <profile> futures position list
100x --profile <profile> futures order list
```

Report:

- Available, frozen, margin, total balance, unrealized PnL, and transferable balance.
- Positions grouped by symbol and side, with leverage, size, entry, liquidation price,
  margin, unrealized PnL, ROE, SL, and TP.
- Open orders that may increase, reduce, or close exposure.

For triggers, use a symbol from the user's question or from open positions/orders:

```bash
100x --profile <profile> futures trigger list BTCUSDT
```

If there are many symbols, inspect triggers only for the symbols relevant to the
answer instead of scanning everything.

## Symbol Drilldown

For "show BTC", "what happened on BTCUSDT", or a specific symbol investigation:

```bash
100x --profile <profile> futures position list --symbol BTCUSDT
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures trigger list BTCUSDT
100x --profile <profile> futures preference BTCUSDT
```

Explain the current state first, then mention pending orders/triggers that could
change that state.

## Order or Trigger Investigation

For "what is this order", "why is there a pending order", or cancel verification:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures order list --symbol BTCUSDT --finished
100x --profile <profile> futures order show BTCUSDT <order-id>
100x --profile <profile> futures trigger list BTCUSDT
100x --profile <profile> futures trigger list BTCUSDT --finished
```

Prefer `order list` and `trigger list` for current open state. `order show` can still
show an order record after it is no longer open.

## History and Fills

For "what happened", "recent fills", "closed positions", or PnL investigation:

```bash
100x --profile <profile> futures order deals --symbol BTCUSDT
100x --profile <profile> futures position history --symbol BTCUSDT
100x --profile <profile> futures order list --symbol BTCUSDT --finished --since now-24h --page-size 50
```

Keep open state separate from historical state. Do not describe finished orders,
canceled triggers, or closed positions as currently active.

## Narrow Reads

```bash
100x --profile <profile> futures balance list --currency USDT
100x --profile <profile> futures position list --symbol BTCUSDT
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures trigger list BTCUSDT
100x --profile <profile> futures preference BTCUSDT
```

Use JSON only when calculating, filtering, or comparing:

```bash
100x --profile <profile> --json futures position list --symbol BTCUSDT
100x --profile <profile> --json futures order list --symbol BTCUSDT --jq 'map({order_id, side, price, volume, status})'
```

## Interpretation Rules

- Distinguish current state from history: open positions/orders/triggers are live;
  finished orders, fills, and position history are past events.
- Treat active triggers as future actions that may change exposure.
- Check preferences when leverage or margin mode matters to the user's question.
- If a symbol has both long and short positions, include position ids so the user can
  tell them apart.
- Do not recommend an action from this skill. If an action is requested, hand off to
  `100x-trade` with the relevant ids and current state.

## CLI Notes

`futures trigger list` takes the symbol as a positional argument. Do not pass
`--symbol` to that command.
