# Position and Risk Workflows

Use these workflows only after the pre-write checklist in `100x-trade/SKILL.md`.

## Inspect Before Acting

Always read current state before closing, changing margin, or changing risk:

```bash
100x --profile <profile> futures position list
100x --profile <profile> futures preference BTCUSDT
```

## Close Position

`--position-id` is required whenever the symbol has more than one position
(long + short hedge, or multiple isolated positions). Without it, the CLI picks
one position and a hedge close can hit the wrong side. Read `position list`
first to pick the id explicitly even when only one position exists.

For a market close, confirm the symbol, position id, profile, market execution
risk, and that market close always closes the full position. Do not describe
market close as partial. Append `--yes` only in the same turn the user
explicitly confirms this exact close.

```bash
100x --profile <profile> futures position close BTCUSDT --position-id <position-id> --type market   # add --yes after explicit confirmation
```

For a limit close, confirm the limit price.

```bash
100x --profile <profile> futures position close BTCUSDT --position-id <position-id> --price 72000 --size 0.001
```

Verify:

```bash
100x --profile <profile> futures position list
100x --profile <profile> futures order list --symbol BTCUSDT
```

## Position Stop Loss or Take Profit

Attach risk controls to an existing position. Per `trigger attach position --help`,
passing one side preserves the other automatically; passing both sides in one
call is one request instead of two. Prefer the single call when setting both,
mainly for fewer round trips.

```bash
# Both sides in one call (one request)
100x --profile <profile> futures trigger attach position BTCUSDT <position-id> --sl-price 65000 --tp-price 75000

# One side; the other side is preserved automatically
100x --profile <profile> futures trigger attach position BTCUSDT <position-id> --sl-price 65000
```

Verify:

```bash
100x --profile <profile> futures trigger list BTCUSDT
```

## Margin

`position margin` reads or adjusts isolated-position margin (per CLI help).
`--add` / `--reduce` amounts are in the position's margin currency
(e.g. USDT for USDT-margined contracts), not contract size.

Read the position first. The command takes the symbol positionally and accepts
`--position-id` when multiple positions match the same symbol.

```bash
100x --profile <profile> futures position list
100x --profile <profile> futures position margin BTCUSDT --position-id <position-id>
100x --profile <profile> futures position margin BTCUSDT --position-id <position-id> --add 10
100x --profile <profile> futures position margin BTCUSDT --position-id <position-id> --reduce 10
```

If the CLI rejects the call (position not found, mode mismatch, etc.), report
the exact error and stop; do not retry as a different symbol, profile, or
position id without user confirmation.

## Preferences

Read current preferences before changing leverage or margin mode.

```bash
100x --profile <profile> futures preference BTCUSDT
```

Change only the requested field, or both fields when the user explicitly asks:

```bash
100x --profile <profile> futures preference BTCUSDT --leverage 25
100x --profile <profile> futures preference BTCUSDT --mode CROSS
100x --profile <profile> futures preference BTCUSDT --leverage 25 --mode CROSS
```

Only change preferences after explicit confirmation. If the CLI reports that
preferences cannot be adjusted, report the message and inspect open positions or
orders before proposing next steps.

## Verification

After risk changes, verify with the relevant current-state reads:

```bash
100x --profile <profile> futures position list
100x --profile <profile> futures preference BTCUSDT
100x --profile <profile> futures trigger list BTCUSDT
```
