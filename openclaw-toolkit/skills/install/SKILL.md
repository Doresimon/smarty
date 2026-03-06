---
name: OpenClaw Install
description: >
  Guides the user through installing OpenClaw, the self-hosted personal AI assistant.
  Activate when the user says: "install OpenClaw", "set up OpenClaw", "how do I get OpenClaw running",
  "OpenClaw onboarding", "install openclaw on Windows/macOS/Linux", "openclaw setup wizard".
version: 1.0.0
---

# OpenClaw Installation

OpenClaw is a self-hosted personal AI assistant that connects to messaging channels (WhatsApp, Telegram, Slack, Discord, and more). Install it using one of the methods below based on the user's platform and preference.

## System Requirements

- **Node.js 22+** (the installer handles this automatically if missing)
- macOS, Linux, or Windows (WSL2 strongly recommended on Windows)
- `pnpm` is required only when building from source

## Recommended: Installer Script

This is the fastest path. The script installs Node if needed, installs OpenClaw, and launches the onboarding wizard.

**macOS / Linux / WSL2:**
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Windows (PowerShell):**
```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

To skip the onboarding wizard and install headlessly:
```bash
# Unix
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --no-onboard

# Windows
& ([scriptblock]::Create((iwr -useb https://openclaw.ai/install.ps1))) -NoOnboard
```

## Alternative: npm / pnpm

Use when the installer script is not available or when the user wants manual control.

**npm:**
```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

**pnpm:**
```bash
pnpm add -g openclaw@latest
pnpm approve-builds -g
openclaw onboard --install-daemon
```

## Alternative: Build from Source

Use when the user wants the latest unreleased code or needs to modify OpenClaw.

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
pnpm ui:build
pnpm build
pnpm link --global
openclaw onboard --install-daemon
```

## Other Runtimes & Platforms

- **Docker / Podman / Nix** — Container-based install options are available
- **Bun** — Supports CLI-only mode (no daemon)
- **Ansible** — For fleet provisioning across multiple machines

## Onboarding Wizard

After installation, the onboarding wizard (`openclaw onboard`) walks through:
1. API key configuration for the AI model
2. Gateway port and binding settings
3. First messaging channel setup
4. Workspace and skills directory selection

Run it at any time to reconfigure:
```bash
openclaw onboard
```

## Post-Installation Verification

After setup, confirm everything is working:

```bash
openclaw doctor     # Diagnose config issues and security warnings
openclaw status     # Check if the gateway is running
openclaw dashboard  # Open the browser-based UI
```

If `openclaw doctor` reports issues, refer to the `openclaw:config` skill for configuration guidance.

## Environment Variables

Override default paths by setting these before running OpenClaw:

| Variable | Purpose |
|---|---|
| `OPENCLAW_HOME` | Override the default `~/.openclaw` directory |
| `OPENCLAW_STATE_DIR` | Override state/session storage location |
| `OPENCLAW_CONFIG_PATH` | Override the config file path |

## References

- Official docs: https://docs.openclaw.ai/install
- GitHub: https://github.com/openclaw/openclaw
- Getting started: https://docs.openclaw.ai/start/getting-started
