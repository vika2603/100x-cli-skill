---
name: 100x-profile
description: "Set up and inspect 100x CLI credential profiles, choose the default profile, and select output modes. Use before private account workflows or when the user asks which 100x profile is active."
---

# 100x Profile

Use this skill when the user needs to configure or inspect local `100x` CLI access.
For write commands (in `100x-trade`), do not assume a profile — require the user
to name one. For read commands (in `100x-account`), it is fine to fall back to
the CLI's current default profile and tell the user which one resolved.

## Flow

1. Check the CLI is available when needed:

```bash
100x version
```

2. Inspect configured profiles without exposing secrets:

```bash
100x profile current
100x profile list
```

3. Show one profile only when the user names it:

```bash
100x profile show <profile>
```

4. Add or update credentials only after the user asks to configure access:

```bash
100x profile add <profile> --client-id <client-id>
```

The CLI prompts for the secret. Do not ask the user to paste secrets into chat.

5. Switch the default profile only after the user asks:

```bash
100x profile use <profile>
```

## Output Modes

Use table output for humans. Use JSON when the next step needs structured data:

```bash
100x --json profile list
100x --json --jq '.[] | .name' profile list
```

For private account commands, if the user does not name a profile, use the CLI's
current default profile and say that you are doing so. For trade or risk-changing
commands, require an explicit profile in the user's confirmation.
