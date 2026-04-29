# Position and Risk Workflows

Use these workflows only after the safety gate in `100x-trade/SKILL.md`.

## Inspect Before Acting

Always read current state before closing, changing margin, or changing risk:

```bash
100x --profile <profile> futures position list
100x --profile <profile> futures preference BTCUSDT
```

## Close Position

For a market close, confirm the symbol, position id, profile, market execution risk,
and that market close always closes the full position. Do not describe market close
as partial.

```bash
100x --profile <profile> futures position close BTCUSDT --position-id <position-id> --type market --yes
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

Attach risk controls to an existing position:

```bash
100x --profile <profile> futures trigger attach position BTCUSDT <position-id> --sl-price 65000
100x --profile <profile> futures trigger attach position BTCUSDT <position-id> --tp-price 75000
```

Verify:

```bash
100x --profile <profile> futures trigger list BTCUSDT
```

## Margin

Read the position first. `position margin` takes the symbol and can take
`--position-id` when multiple positions match the same symbol.

```bash
100x --profile <profile> futures position list
100x --profile <profile> futures position margin BTCUSDT --position-id <position-id>
100x --profile <profile> futures position margin BTCUSDT --position-id <position-id> --add 10
100x --profile <profile> futures position margin BTCUSDT --position-id <position-id> --reduce 10
```

If the CLI reports that the position does not exist, do not retry as a different
symbol or profile without user confirmation.

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
