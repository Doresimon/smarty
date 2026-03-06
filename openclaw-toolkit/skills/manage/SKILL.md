---
name: OpenClaw Manage
description: >
  Covers starting, stopping, restarting, and monitoring the OpenClaw gateway and daemon.
  Also covers updating, diagnosing, and checking service health.
  Activate when the user says: "start OpenClaw", "stop OpenClaw", "restart OpenClaw",
  "openclaw daemon", "openclaw status", "openclaw doctor", "openclaw not running",
  "openclaw update", "openclaw logs", "openclaw dashboard", "is OpenClaw running".
version: 1.0.0
---

# OpenClaw Service Management

OpenClaw runs as a Gateway daemon registered with the system service manager (launchd on macOS, systemd on Linux). Use the CLI to manage it.

## Checking Status

```bash
openclaw status        # Show gateway running state and connection info
openclaw doctor        # Full diagnostic: config, security, migrations, warnings
openclaw dashboard     # Open browser-based UI at http://localhost:18789
```

Use `openclaw doctor` as the first step when troubleshooting any issue.

## Starting the Gateway

**Via daemon (recommended):**
The daemon starts automatically on login after install. To start it manually:
```bash
openclaw gateway --install-daemon   # Install and start daemon
```

**Foreground mode (for debugging):**
```bash
openclaw gateway --port 18789 --verbose
```

Press `Ctrl+C` to stop foreground mode.

## Stopping the Gateway

```bash
openclaw gateway --uninstall-daemon   # Stop and remove daemon
```

To temporarily stop without uninstalling:
- On macOS (launchd): `launchctl stop com.openclaw.gateway`
- On Linux (systemd): `systemctl --user stop openclaw-gateway`

## Restarting the Gateway

```bash
openclaw gateway --restart   # Restart the running daemon
```

Or from within a messaging channel (owner only in groups):
```
/restart
```

## Sending Test Messages

Send a message directly from the CLI to test the gateway:
```bash
openclaw message send --to +1234567890 --message "hello"
```

Chat with the agent directly:
```bash
openclaw agent --message "what can you do?" --thinking high
```

## Updating OpenClaw

```bash
openclaw update                    # Update to latest on current channel
openclaw update --channel stable   # Switch to stable channel and update
openclaw update --channel beta     # Switch to beta
openclaw update --channel dev      # Switch to dev (latest main)
```

After updating, run `openclaw doctor` to apply any pending config migrations.

## Node Management

OpenClaw supports remote device nodes (macOS app, iOS, Android):

```bash
openclaw nodes list       # List active connected nodes
openclaw nodes describe   # Show node capabilities and permissions
```

## Diagnostics & Troubleshooting

### Common issues

| Symptom | Fix |
|---|---|
| Gateway not starting | Run `openclaw doctor` — check for config errors |
| Bot not responding in chat | Check `openclaw status` — verify daemon is running |
| DM from unknown sender ignored | Expected — run `openclaw pairing approve <channel> <code>` |
| Port conflict | Change `gateway.port` in config and restart |
| High memory/CPU | Check skills and automations; disable unused channels |

### Logging

Logs location varies by platform. Check `openclaw doctor` output for the log file path.

Start with verbose logging for debugging:
```bash
openclaw gateway --verbose
```

## Automations

OpenClaw supports scheduled and event-driven automations:
- **Cron** — Scheduled tasks
- **Webhooks** — Trigger on external HTTP events
- **Gmail Pub/Sub hooks** — React to incoming email

See https://docs.openclaw.ai/automation/ for setup.

## References

- Updating: https://docs.openclaw.ai/install/updating
- Logging: https://docs.openclaw.ai/logging
- Automation: https://docs.openclaw.ai/automation
