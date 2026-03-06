---
name: OpenClaw Config
description: >
  Explains how to configure OpenClaw, including the gateway, openclaw.json settings,
  security hardening, channel configuration, and workspace options.
  Activate when the user says: "configure OpenClaw", "openclaw.json", "openclaw gateway settings",
  "openclaw security settings", "openclaw bind address", "openclaw port", "openclaw workspace",
  "openclaw config file", "openclaw Tailscale", "openclaw channel config".
version: 1.0.0
---

# OpenClaw Configuration

OpenClaw configuration lives in `~/.openclaw/`. The main configuration is managed through the onboarding wizard or by editing config files directly. Override the config path with `OPENCLAW_CONFIG_PATH`.

## Gateway Configuration

The gateway is the OpenClaw control plane. Configure it for binding, port, auth, and exposure.

### Port & Binding

Default: `ws://127.0.0.1:18789`

| Setting | Description | Default |
|---|---|---|
| `gateway.port` | Port the gateway listens on | `18789` |
| `gateway.bind` | Bind address | `127.0.0.1` (loopback) |

**Security rule**: Keep `gateway.bind` set to `127.0.0.1`. Never bind to `0.0.0.0` unless you understand the exposure risk. Never expose the gateway directly to the internet.

Start the gateway manually:
```bash
openclaw gateway --port 18789 --verbose
```

### Authentication

For remote access scenarios (Tailscale Funnel/Serve), enable password auth:

```json
{
  "gateway": {
    "auth": {
      "mode": "password"
    }
  }
}
```

Disable Tailscale identity header trust if not using Tailscale:
```json
{
  "gateway": {
    "auth": {
      "allowTailscale": false
    }
  }
}
```

### Tailscale Exposure

Use Tailscale to expose the gateway securely without opening ports:

| Mode | Description |
|---|---|
| `off` | No automation (default) |
| `serve` | Expose via Tailscale Serve (tailnet only) |
| `funnel` | Expose publicly via Tailscale Funnel (requires password auth) |

```json
{
  "gateway": {
    "tailscale": {
      "mode": "serve",
      "resetOnExit": true
    }
  }
}
```

When using `serve` or `funnel`, the gateway must bind to loopback (`127.0.0.1`).

## Channel Configuration

OpenClaw supports 20+ messaging channels. Configure them via the onboarding wizard or config file.

Supported channels include: WhatsApp (Baileys), Telegram (grammY), Slack (Bolt), Discord (discord.js), Google Chat, Signal (signal-cli), iMessage (BlueBubbles), IRC, Microsoft Teams, Matrix, LINE, Mattermost, Twitch, and more.

### DM Policy

Control who can message the bot:

```json
{
  "dmPolicy": "pairing"
}
```

| Policy | Behavior |
|---|---|
| `pairing` | Unknown senders receive a pairing code; message is not processed (default, safest) |
| `open` | Any sender can message; requires `allowList` with `"*"` (explicit opt-in) |

Approve a pairing code:
```bash
openclaw pairing approve <channel> <code>
```

### Per-Channel Allowlist

Restrict which users can interact with the bot per channel:

```json
{
  "channels": {
    "discord": {
      "allowFrom": ["user1#1234", "user2#5678"]
    },
    "slack": {
      "dmPolicy": "allowList"
    }
  }
}
```

## Workspace & Skills Directory

The agent's working directory defaults to `~/.openclaw/workspace`. Override it:

```json
{
  "agents": {
    "defaults": {
      "workspace": "/custom/path/to/workspace"
    }
  }
}
```

Skills are stored in `~/.openclaw/workspace` by default. Install skills from ClawHub:
```bash
openclaw skills list
openclaw skills install <skill-name>
```

## Session Defaults

Configure default behavior for new sessions:

Chat commands available in messaging channels:
- `/status` — Show model, token count, cost
- `/new` or `/reset` — Start a new session
- `/compact` — Summarize and compress context
- `/think <level>` — Set thinking level: `off`, `minimal`, `low`, `medium`, `high`, `xhigh`
- `/verbose on|off` — Toggle verbose output
- `/activation mention|always` — Control group chat activation

## Update Channel

Switch between release channels:

```bash
openclaw update --channel stable   # Tagged releases (default, recommended)
openclaw update --channel beta     # Prerelease builds
openclaw update --channel dev      # Latest main branch
```

## Validating Configuration

Always run after config changes:
```bash
openclaw doctor
```

`doctor` checks for:
- Missing required config values
- Risky DM policy configurations
- Security issues (open bindings, exposed ports)
- Pending migrations

## References

- Security docs: https://docs.openclaw.ai/gateway/security
- Onboarding docs: https://docs.openclaw.ai/start/onboarding
- Full config reference: https://docs.openclaw.ai
