---
name: 100x-trade
description: "Execute 100x CLI workflows that change account state: placing, editing, canceling, or closing orders, triggers, positions, margin, leverage, margin mode, or risk settings. Use only after explicit user confirmation."
---

# 100x Trade

Use this skill for any `100x` CLI workflow that can change account state. Require an
explicit profile and a confirmation that names the concrete action, symbol or market,
and the fields that apply to that action.

## Safety Gate

Before running a write command, confirm:

- Profile, never inferred for writes.
- Symbol or market.
- Direction or side when trading.
- Size and unit when the command accepts size.
- Order type, price, trigger price, or market execution when placing or closing.
- Full-close behavior when using market position close.
- Risk impact for leverage, margin mode, margin, stop loss, or take profit.

Read-only commands may be used to prepare a plan. Network failures on writes are
ambiguous; verify account state before retrying.

## Size Handling

If the user gives a notional value such as "100 USDT", do not treat it as contract
size. Read the current market price, calculate an approximate size, show the formula
and rounding, and ask for confirmation before executing.

## References

Open only the reference that matches the user's task:

- `references/order-workflows.md` for new orders, edits, cancels, triggers, and fill checks.
- `references/position-risk-workflows.md` for position close, position TP/SL, margin, leverage, and preferences.

## Verification

After every write, run the matching read command:

- New or edited order: `futures order list --symbol <symbol>`
- Canceled order: `futures order list --symbol <symbol>`
- Trigger change: `futures trigger list <symbol>`
- Position change: `futures position list`
- Fills or market execution: `futures order deals --symbol <symbol>`

Report the command result, not just that the command exited.
