# 100x CLI Skills

Agent skills for helping users operate the installed `100x` command-line client.

The repository follows the standard skills layout: each installable skill lives under
`skills/<skill-name>/SKILL.md`.

## Skills

- `100x-profile`: configure profiles, choose the active account, and select output modes.
- `100x-market`: read public market data without requiring account credentials.
- `100x-account`: inspect account health, exposure, pending activity, fills, and history.
- `100x-trade`: place, edit, cancel, and verify orders, triggers, positions, margin, and risk settings.

## Install

List available skills:

```bash
npx skills add vika2603/100x-cli-skill --list
```

Install selected skills for Codex:

```bash
npx skills add vika2603/100x-cli-skill --skill 100x-market --skill 100x-account --agent codex -g
```

Install all skills:

```bash
npx skills add vika2603/100x-cli-skill --skill '*' --agent codex -g -y
```

The skills assume the user already has the `100x` CLI installed. They describe CLI
workflows for end users and intentionally avoid implementation details.

`--all` is intentionally not used here because it installs every skill to every
detected agent.
