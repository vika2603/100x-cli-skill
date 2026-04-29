---
name: 100x-trade
description: "Execute 100x CLI workflows that change account state: placing, editing, canceling, or closing orders, triggers, positions, margin, leverage, margin mode, or risk settings. Use only after explicit user confirmation."
---

# 100x Trade

Use this skill for any `100x` CLI workflow that can change account state.
Confirm an explicit profile and the concrete action — symbol or market and the
fields that apply to that action — before running a write.

This skill is process guidance for the agent, not a technical guard. It lowers
the chance of an unintended write but does not prevent one. Real enforcement
(deny-by-default permissions, PreToolUse hooks, command wrappers, size caps,
daily caps) belongs in the harness or in a wrapper around the `100x` CLI.

## Pre-Write Checklist

Confirm before running a write:

- Profile. Do not infer for writes. If the user has not named one, run
  `100x profile list` and show the configured profiles (note the current
  default) so they can pick.
- Symbol or market.
- Direction or side when trading.
- Size and unit when the command accepts size.
- Order type, price, trigger price, or market execution when placing or closing.
- Full-close behavior when using market position close.
- Risk impact for leverage, margin mode, margin, stop loss, or take profit.

Read-only commands may be used freely to prepare a plan.

## Network Failures and Retries

Network failures on writes are ambiguous: the request may have landed even if
the CLI returned an error. Before retrying, verify with the matching read
(`futures order list`, `futures order deals`, `futures position list`,
`futures trigger list <symbol>`). If a retry is genuinely needed for
`futures order place` or `futures position close`, pass `--client-order-id <id>`
so the retry is idempotent on the exchange side.

## `--yes`

`--yes` is the non-interactive confirmation flag. Append it to a write command
in the same turn the user explicitly confirmed that exact action. Templates
in the references show commands without `--yes` so it is not carried into a
different action or a later turn by accident.

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
