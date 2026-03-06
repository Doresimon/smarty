---
name: openclaw:setup
description: Interactive setup wizard for OpenClaw — walks through installation, configuration, and credentials step by step.
argument-hint: "[--skip-install] [--skip-config] [--skip-credentials]"
allowed-tools:
  - Bash
  - AskUserQuestion
---

Guide the user through a complete OpenClaw setup in three sequential phases: install, config, and credentials. Be conversational, check in after each phase, and skip phases the user indicates are already done.

## Behavior

Parse any arguments passed by the user:
- `--skip-install` — skip Phase 1
- `--skip-config` — skip Phase 2
- `--skip-credentials` — skip Phase 3

If no arguments, run all three phases in order.

---

## Phase 1: Installation

Unless `--skip-install` was passed:

1. Ask: "Is OpenClaw already installed on your system?"
   - If yes, skip to Phase 2.
   - If no, continue.

2. Ask: "What platform are you on?" (macOS/Linux/WSL2, or Windows PowerShell)

3. Provide the appropriate install command:

   **macOS / Linux / WSL2:**
   ```bash
   curl -fsSL https://openclaw.ai/install.sh | bash
   ```

   **Windows (PowerShell):**
   ```powershell
   iwr -useb https://openclaw.ai/install.ps1 | iex
   ```

   Alternative if the script fails:
   ```bash
   npm install -g openclaw@latest
   openclaw onboard --install-daemon
   ```

4. Ask the user to run the command and confirm it succeeded.

5. After install, verify:
   ```bash
   openclaw doctor
   openclaw status
   ```

   Show the user the output and flag any errors before proceeding.

---

## Phase 2: Configuration

Unless `--skip-config` was passed:

1. Ask: "Do you want to use the default gateway settings (port 18789, localhost only)?"
   - If yes, confirm the defaults are secure and move on.
   - If no, ask what they want to change (port, Tailscale exposure, auth mode).

2. Walk through DM policy:
   - Explain the difference between `pairing` (safe default) and `open`.
   - Recommend `pairing` unless the user has a specific reason to use `open`.

3. Ask: "Which messaging channels do you want to connect?" (WhatsApp, Telegram, Slack, Discord, or other)
   - Note which ones they selected — credentials for these will be covered in Phase 3.

4. If any config changes were made, remind the user to run:
   ```bash
   openclaw doctor
   ```

---

## Phase 3: Credentials

Unless `--skip-credentials` was passed:

1. Start with the AI model API key:
   - Ask: "Which AI provider are you using?" (Anthropic, OpenAI, or other)
   - Provide the appropriate env var:
     - Anthropic: `export ANTHROPIC_API_KEY=sk-ant-...`
     - OpenAI: `export OPENAI_API_KEY=sk-...`
   - Recommend running `openclaw onboard` to store the key persistently.

2. For each messaging channel the user selected in Phase 2, walk through credentials one at a time:

   **WhatsApp:** QR code pairing — run `openclaw onboard`, select WhatsApp, scan QR with phone.

   **Telegram:**
   - Message `@BotFather` on Telegram → `/newbot` → copy the token.
   - Add via `openclaw onboard` or config: `channels.telegram.token`.

   **Slack:**
   - Create app at https://api.slack.com/apps
   - Enable Socket Mode → get App-Level Token (`xapp-...`)
   - Add scopes: `chat:write`, `im:history`, `im:read`, `app_mentions:read`
   - Install to workspace → get Bot Token (`xoxb-...`)
   - Add both tokens via `openclaw onboard`.

   **Discord:**
   - Create bot at https://discord.com/developers/applications
   - Enable `Message Content Intent` and `Server Members Intent`
   - Copy bot token, invite bot to server
   - Add token via `openclaw onboard`.

   For other channels: direct the user to run `openclaw onboard` and select the channel.

3. After all credentials are set, run a final check:
   ```bash
   openclaw doctor
   openclaw status
   ```

---

## Completion

Once all phases are done:
- Summarize what was set up (platform, channels connected, model provider).
- Remind the user they can reconfigure at any time with `openclaw onboard`.
- Suggest testing by sending a message on one of the connected channels.
