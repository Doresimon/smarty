---
name: openclaw:setup
description: Interactive setup wizard for OpenClaw — walks through installation, configuration, and credentials step by step.
argument-hint: "[--skip-install] [--skip-config] [--skip-credentials]"
allowed-tools:
  - Bash
  - AskUserQuestion
---

Guide the user through a complete OpenClaw setup in three sequential phases: install, config, and credentials. Be conversational, check in after each phase, and skip phases the user indicates are already done.

When asking questions, always use `AskUserQuestion` with structured options:
- Yes/No questions: provide options `["Yes", "No"]`
- Single-choice questions: provide a selection list; always include a final option like `"Other / enter manually"` that accepts free text input
- Multi-select questions: ask one item at a time with checkboxes, OR present a numbered list and ask the user to type the numbers they want (e.g. "1,3,4"), always including an "Other" option

---

## Behavior

Parse any arguments passed by the user:
- `--skip-install` — skip Phase 1
- `--skip-config` — skip Phase 2
- `--skip-credentials` — skip Phase 3

If no arguments, run all three phases in order.

---

## Phase 1: Installation

Unless `--skip-install` was passed:

1. Ask (Yes/No): "Is OpenClaw already installed on your system?"
   - Yes → skip to Phase 2.
   - No → continue.

2. Ask (selection list): "What platform are you on?"
   Options: `["macOS / Linux / WSL2", "Windows (PowerShell)", "Other / enter manually"]`

3. Provide the appropriate install command based on their selection:

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

4. Ask (Yes/No): "Did the install command succeed?"
   - No → show the alternative `npm install -g openclaw@latest` approach and ask again.

5. After install, verify:
   ```bash
   openclaw doctor
   openclaw status
   ```

   Show the user the output and flag any errors before proceeding.

---

## Phase 2: Configuration

Unless `--skip-config` was passed:

1. Ask (Yes/No): "Do you want to use the default gateway settings (port 18789, localhost only)?"
   - Yes → confirm the defaults are secure and move on.
   - No → ask what they want to change using a selection list: `["Port number", "Tailscale exposure", "Auth mode", "Other / enter manually"]`

2. Walk through DM policy with a selection:
   - Briefly explain: `pairing` (safe default — only paired devices can DM) vs `open` (any user can message).
   - Ask (selection list): "Which DM policy do you want?"
     Options: `["pairing (recommended)", "open", "Tell me more before deciding"]`
   - If "Tell me more", explain further then ask again.

3. Ask (multi-select): "Which messaging channels do you want to connect?"
   Present as a numbered list:
   ```
   1. WhatsApp
   2. Telegram
   3. Slack
   4. Discord
   5. Other
   ```
   Ask: "Enter the numbers of the channels you want (e.g. 1,3), or type channel names if choosing Other."
   Note which ones they selected — credentials for these will be covered in Phase 3.

4. If any config changes were made, remind the user to run:
   ```bash
   openclaw doctor
   ```

---

## Phase 3: Credentials

Unless `--skip-credentials` was passed:

1. Start with the AI model API key:
   - Ask (selection list): "Which AI provider are you using?"
     Options: `["Anthropic (Claude)", "OpenAI (GPT)", "Other / enter manually"]`
   - Provide the appropriate env var:
     - Anthropic: `export ANTHROPIC_API_KEY=sk-ant-...`
     - OpenAI: `export OPENAI_API_KEY=sk-...`
     - Other: ask the user to provide the env var name and value.
   - Recommend running `openclaw onboard` to store the key persistently.
   - Ask (Yes/No): "Have you added the API key?"

2. For each messaging channel the user selected in Phase 2, walk through credentials one at a time. After presenting each set of instructions, ask (Yes/No): "Done — ready to move on?"

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
- Ask (selection list): "What would you like to do next?"
  Options: `["Send a test message on a connected channel", "View openclaw status", "I'm done — nothing else", "Other / enter manually"]`
