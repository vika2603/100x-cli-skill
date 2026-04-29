# Order and Trigger Workflows

Use these workflows only after the safety gate in `100x-trade/SKILL.md`.

## Limit Order

Confirm profile, symbol, side, size, and limit price.

```bash
100x --profile <profile> futures order place --limit --symbol BTCUSDT --side buy --size 0.001 --price 70000
```

Verify:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
```

## Market Order

Confirm the user accepts market execution.

```bash
100x --profile <profile> futures order place --market --symbol BTCUSDT --side buy --size 0.001
```

Verify fills and resulting position:

```bash
100x --profile <profile> futures order deals --symbol BTCUSDT
100x --profile <profile> futures position list
```

## Edit Open Limit Order

Read current open orders first, then edit by order id.

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
100x --profile <profile> futures order edit BTCUSDT <order-id> --price 70500 --size 0.001
```

Verify with `order list`.

## Cancel Orders

Cancel one order:

```bash
100x --profile <profile> futures order cancel BTCUSDT <order-id>
```

Cancel all open orders in a market:

```bash
100x --profile <profile> futures order cancel-all BTCUSDT
```

Verify with:

```bash
100x --profile <profile> futures order list --symbol BTCUSDT
```

## Attach Stop Loss or Take Profit to an Order

Use the order id returned by `order place` or found with `order list`.
Order-level SL/TP is shared across all open orders on the same position, so confirm
that the user accepts the position-level effect before attaching or changing it.

```bash
100x --profile <profile> futures trigger attach order BTCUSDT <order-id> --sl-price 65000
100x --profile <profile> futures trigger attach order BTCUSDT <order-id> --tp-price 75000
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
