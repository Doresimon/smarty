# smarty

Aji's Claude Code plugin collection.

## Install

```bash
# Add this marketplace
claude plugin marketplace add Doresimon/smarty

# Install a plugin
claude plugin install openclaw-toolkit@smarty
claude plugin install claude-toolkit@smarty

# Update
claude plugin marketplace update smarty
claude plugin update openclaw-toolkit@smarty
claude plugin update claude-toolkit@smarty
```

---

## Plugins

### openclaw-toolkit

Skills and a setup wizard for [OpenClaw](https://openclaw.ai) — the self-hosted personal AI assistant.

**Install:**
```bash
claude plugin install openclaw-toolkit@smarty
```

**Commands:**
- `/openclaw:setup` — Interactive setup wizard: installs OpenClaw, configures the gateway, and walks through credentials for each messaging channel (WhatsApp, Telegram, Slack, Discord, and more).

**Skills (auto-activate):**
- `openclaw-install` — Guides installation on macOS, Linux, WSL2, or Windows
- `openclaw-config` — Explains gateway settings, security, and channel configuration
- `openclaw-manage` — Starting, stopping, updating, and monitoring the OpenClaw daemon
- `openclaw-credentials` — Setting up API keys, OAuth, and per-channel tokens. Detects and reuses your existing Claude Code API key from `~/.claude.json` automatically.

---

### claude-toolkit

Browse the Claude Code plugin marketplace and install plugins interactively.

**Install:**
```bash
claude plugin install claude-toolkit@smarty
```

**Commands:**
- `/claude-toolkit:setup` — Marketplace setup wizard: adds the `Doresimon/smarty` marketplace if missing, lets you browse or search for plugins, select from a list, and installs them — all without leaving Claude.
