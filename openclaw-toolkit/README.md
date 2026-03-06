# openclaw-toolkit

A Claude Code plugin providing skills for installing, configuring, and managing [OpenClaw](https://github.com/openclaw/openclaw) — the self-hosted personal AI assistant.

## Features

| Skill | What it covers |
|---|---|
| `openclaw-toolkit:install` | Installation (script, npm, pnpm, from source), onboarding wizard, verification |
| `openclaw-toolkit:config` | Gateway settings, security hardening, channel config, workspace, DM policy |
| `openclaw-toolkit:manage` | Start/stop/restart daemon, status checks, updates, diagnostics, troubleshooting |
| `openclaw-toolkit:credentials` | AI model API keys, OAuth, per-channel tokens (WhatsApp, Telegram, Slack, Discord, etc.) |

## Installation

Copy this plugin to your Claude Code plugins directory or install via the marketplace.

```bash
# Local install — point Claude Code to this directory
cc --plugin-dir /path/to/openclaw-toolkit
```

## Usage

Skills activate automatically when you ask Claude about OpenClaw. Example prompts:

- "How do I install OpenClaw on macOS?"
- "How do I configure the OpenClaw gateway?"
- "How do I start and stop the OpenClaw daemon?"
- "How do I connect OpenClaw to Telegram?"

## Requirements

No additional tools required. All knowledge is embedded in the skills.

## Author

Aji <doresimon1993@gmail.com>

## License

MIT
