---
name: openclaw-credentials
description: >
  Guides the user through setting up API keys, model authentication, and messaging channel
  credentials for OpenClaw. Covers OAuth, API key rotation, model failover, and per-channel auth.
  Activate when the user says: "OpenClaw API key", "OpenClaw authentication", "connect OpenClaw to WhatsApp",
  "OpenClaw Telegram setup", "OpenClaw Slack token", "OpenClaw Discord bot", "openclaw model auth",
  "openclaw credentials", "openclaw OAuth", "openclaw channel token", "set up OpenClaw channels".
version: 1.0.0
---

# OpenClaw Credentials & Authentication

OpenClaw requires two types of credentials:
1. **AI model credentials** — API keys or OAuth tokens for the AI provider (e.g., Anthropic, OpenAI)
2. **Channel credentials** — Tokens and app registrations for each messaging channel

Configure both via the onboarding wizard or by editing config files directly.

## AI Model Credentials

Run the onboarding wizard to configure the model interactively:
```bash
openclaw onboard
```

OpenClaw recommends using the latest-generation model available for best performance and security.

### Reuse Claude Code API Key

If the user already has Claude Code installed, their Anthropic API key is stored in `~/.claude.json` under the `primaryApiKey` field. Offer to read and reuse it automatically:

1. Read `~/.claude.json` via `Bash` and extract `primaryApiKey`:
   ```bash
   node -e "const f=require('fs');const d=JSON.parse(f.readFileSync(require('os').homedir()+'/.claude.json','utf8'));console.log(d.primaryApiKey||'')"
   ```
2. If a key is found, ask (Yes/No): "Found your Claude Code API key (`sk-ant-...xxxx`). Use this for OpenClaw?"
   - Yes → set it for OpenClaw automatically:
     ```bash
     openclaw onboard --anthropic-key <key>
     ```
     Or export it for the current session and run onboard:
     ```bash
     export ANTHROPIC_API_KEY=<key> && openclaw onboard
     ```
   - No → fall back to manual API key entry below.
3. If no key is found in `~/.claude.json`, skip this option and proceed to manual entry.

### API Key Auth

Set the provider API key in config or environment:
```bash
export ANTHROPIC_API_KEY=sk-ant-...
export OPENAI_API_KEY=sk-...
```

Or configure via the onboarding wizard which stores keys in `~/.openclaw/`.

### OAuth Auth

OAuth is supported for providers that offer it. Configure via the onboarding wizard — it handles the OAuth flow automatically.

### Model Failover

Configure multiple model profiles with fallback:
- Primary model attempts first
- Falls back to secondary on rate limits or errors
- Supports mixing OAuth and API key profiles

See: https://docs.openclaw.ai/concepts/model-failover

## Channel Credentials

Each channel requires its own credentials. Use `openclaw onboard` to walk through setup for each channel, or configure manually.

### WhatsApp (Baileys)

WhatsApp uses QR code pairing — no bot account needed:
1. Run `openclaw onboard` and select WhatsApp
2. Scan the QR code with your WhatsApp mobile app
3. Session is saved to `~/.openclaw/`

No API key required. Works with personal WhatsApp accounts.

### Telegram (grammY)

1. Open Telegram and message `@BotFather`
2. Send `/newbot` and follow the prompts to get a bot token
3. Add the token during `openclaw onboard` or in config:
```json
{
  "channels": {
    "telegram": {
      "token": "YOUR_BOT_TOKEN"
    }
  }
}
```

### Slack (Bolt)

1. Create a Slack app at https://api.slack.com/apps
2. Enable Socket Mode and generate an App-Level Token (`xapp-...`)
3. Add Bot Token Scopes: `chat:write`, `im:history`, `im:read`, `app_mentions:read`
4. Install to workspace to get Bot Token (`xoxb-...`)
5. Add both tokens during `openclaw onboard`:
```json
{
  "channels": {
    "slack": {
      "botToken": "xoxb-...",
      "appToken": "xapp-..."
    }
  }
}
```

### Discord (discord.js)

1. Create a bot at https://discord.com/developers/applications
2. Under "Bot", enable `Message Content Intent` and `Server Members Intent`
3. Generate a bot token
4. Invite the bot to your server with the OAuth2 URL Generator (scopes: `bot`, permissions: `Send Messages`, `Read Message History`)
5. Add the token during `openclaw onboard`:
```json
{
  "channels": {
    "discord": {
      "token": "YOUR_BOT_TOKEN"
    }
  }
}
```

### Signal (signal-cli)

1. Install `signal-cli`: https://github.com/AsamK/signal-cli
2. Register a phone number with Signal
3. Link to OpenClaw during onboarding

### iMessage (BlueBubbles)

Requires macOS with iMessage enabled:
1. Install BlueBubbles server on macOS: https://bluebubbles.app
2. Obtain the BlueBubbles server URL and password
3. Configure during `openclaw onboard`

### Microsoft Teams

Requires a Microsoft 365 developer account or org admin access to register a bot application in Azure.

### Other Channels

OpenClaw supports: Google Chat, IRC, Matrix, Feishu, LINE, Mattermost, Nextcloud Talk, Nostr, Synology Chat, Tlon, Twitch, Zalo, WebChat.

Each channel's setup instructions are covered in the onboarding wizard. Run:
```bash
openclaw onboard
```

## Pairing & DM Security

After channel setup, unknown senders get a pairing code by default:
```bash
openclaw pairing approve <channel> <pairing-code>
```

Check for risky auth configurations:
```bash
openclaw doctor
```

## Rotating Credentials

To rotate an API key or channel token:
1. Update the key in your provider's dashboard (or re-read from `~/.claude.json` `primaryApiKey` if using Claude Code)
2. Edit the value in `~/.openclaw/` config or re-run `openclaw onboard`
3. Restart the gateway: `openclaw gateway --restart`
4. Verify: `openclaw doctor`

## References

- Onboarding: https://docs.openclaw.ai/start/onboarding
- Model failover: https://docs.openclaw.ai/concepts/model-failover
- Security: https://docs.openclaw.ai/gateway/security
- Channel docs: https://docs.openclaw.ai
