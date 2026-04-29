# Order and Trigger Workflows

Use these workflows only after the pre-write checklist in `100x-trade/SKILL.md`.

## Limit Order

Confirm profile, symbol, side, size, and limit price. If the user also specified
SL or TP, attach them in the same `order place` call with `--sl-price` and
`--tp-price` — do not place the order first and then attach via
`trigger attach order`, which costs extra requests and can race.

```bash
# Plain limit
100x --profile <profile> futures order place --limit --symbol BTCUSDT --side buy --size 0.001 --price 70000

# Limit with SL and/or TP attached in one call
100x --profile <profile> futures order place --limit --symbol BTCUSDT --side buy --size 0.001 --price 70000 \
  --sl-price 68000 --tp-price 76000
```

Verify:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures trigger list BTCUSDT   # only if SL/TP was attached
```

## Market Order

Confirm the user accepts market execution. SL/TP can be attached the same way
as for limit orders.

```bash
100x --profile <profile> futures order place --market --symbol BTCUSDT --side buy --size 0.001

# Market with SL and TP in one call
100x --profile <profile> futures order place --market --symbol BTCUSDT --side buy --size 0.001 \
  --sl-price 68000 --tp-price 76000
```

Verify fills and resulting position:

```bash
100x --profile <profile> futures order deals --symbol BTCUSDT
100x --profile <profile> futures position list
100x --profile <profile> futures trigger list BTCUSDT   # only if SL/TP was attached
```

## Edit Open Limit Order

Read current open orders first, then edit by order id.

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures order edit BTCUSDT <order-id> --price 70500 --size 0.001
```

`order edit` rebooks the order with a new order id and re-attaches SL/TP only
"when possible". After every edit, verify both the new order id and that SL/TP
carried over:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures trigger list BTCUSDT
```

If SL/TP did not re-attach, re-attach manually with
`futures trigger attach order <symbol> <new-order-id> --sl-price ... --tp-price ...`.

## Cancel Orders

Cancel one order:

```bash
100x --profile <profile> futures order cancel BTCUSDT <order-id>
```

Cancel all open orders in a market. This drops every open order in the symbol,
including any unrelated to the current task. Read the order list first and
confirm the full set with the user before running:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures order cancel-all BTCUSDT
```

Verify with:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
```

## Attach Stop Loss or Take Profit to an Existing Order

Prefer attaching SL/TP at order-place time (see Limit Order / Market Order above).
Use this workflow only when changing SL/TP on an order that is already open.

Use the order id from `order list`. Order-level SL/TP is shared across all open
orders on the same position, so confirm the position-level effect before changing it.

When setting both SL and TP, pass them in a single call. Two separate
`trigger attach order` calls can leave the first side as a standalone trigger
that conflicts with the second call.

```bash
# Both sides in one call (preferred when both are being set)
100x --profile <profile> futures trigger attach order BTCUSDT <order-id> --sl-price 65000 --tp-price 75000

# One side; the other side is preserved automatically
100x --profile <profile> futures trigger attach order BTCUSDT <order-id> --sl-price 65000
```

Verify:

```bash
100x --profile <profile> futures trigger list BTCUSDT
```

## Standalone Trigger

Confirm trigger price, execution behavior, side, size, and symbol.

```bash
100x --profile <profile> futures trigger place BTCUSDT --side buy --size 0.001 --trigger-price 68000
```

Verify with `futures trigger list BTCUSDT`.

## Business Errors

If the CLI returns a business error such as "this market cannot be operated", report
the message and stop. Do not convert it into a different action.
