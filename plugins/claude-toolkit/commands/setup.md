---
name: claude-toolkit:setup
description: Browse the Claude Code marketplace and install plugins interactively.
argument-hint: "[--marketplace <name>] [--search <query>]"
allowed-tools:
  - Bash
  - AskUserQuestion
---

Guide the user through browsing the Claude Code plugin marketplace and installing plugins. Be conversational and use structured selections at every step.

**Command execution rule:** Never ask the user to run commands themselves. Use `Bash` to run all `claude plugin` commands directly. Only ask for confirmation before installing or removing plugins.

When asking questions, always use `AskUserQuestion` with structured options:
- Yes/No questions: options `["Yes", "No"]`
- Single-choice: selection list, always include `"Other / enter manually"`
- Multi-select: numbered list, ask user to type numbers (e.g. `1,3`)

---

## Step 1: Marketplace Selection

1. Run via `Bash` to list available marketplaces:
   ```bash
   claude plugin marketplace list
   ```
   Show the user the output.

2. Check if `Doresimon/smarty` is already in the list.
   - If **not present**, ask (Yes/No): "The `Doresimon/smarty` marketplace is not added yet. Add it now?"
     - Yes → run via `Bash`:
       ```bash
       claude plugin marketplace add Doresimon/smarty
       ```
       Show output, then re-run `claude plugin marketplace list` to confirm it was added.
     - No → continue without adding it.

3. Ask (selection list): "Which marketplace do you want to browse?"
   - Build options from the current marketplace list (after any additions above).
   - Always include `"All marketplaces"` and `"Other / enter manually"`.
   - If user picks "Other", ask them to type the marketplace name or URL.

---

## Step 2: Browse & Search

1. Ask (selection list): "How do you want to find plugins?"
   Options: `["Browse all plugins", "Search by keyword", "I know the plugin name"]`

2. Based on their choice:

   **Browse all:** Run via `Bash`:
   ```bash
   claude plugin marketplace browse [marketplace]
   ```
   Display the list of available plugins with descriptions.

   **Search by keyword:** Ask (free text): "What are you looking for?" then run via `Bash`:
   ```bash
   claude plugin search "<keyword>" [--marketplace <name>]
   ```
   Display results.

   **I know the plugin name:** Ask (free text): "What is the plugin name?" then run via `Bash`:
   ```bash
   claude plugin info "<plugin-name>"
   ```
   Display details.

3. After showing results, ask (Yes/No): "Would you like to search or browse again before selecting?"
   - Yes → repeat Step 2.
   - No → continue to Step 3.

---

## Step 3: Plugin Selection

1. Present the plugins found as a numbered list. For example:
   ```
   1. plugin-name-a — Short description
   2. plugin-name-b — Short description
   3. plugin-name-c — Short description
   ```

2. Ask: "Enter the numbers of the plugins you want to install (e.g. 1,3), or type a plugin name manually."

3. Confirm the selection:
   Ask (Yes/No): "Ready to install: [list of selected plugins]?"
   - No → go back to Step 3.
   - Yes → continue to Step 4.

---

## Step 4: Installation

For each selected plugin, run via `Bash`:
```bash
claude plugin install "<plugin-name>"
```

- Show output after each install.
- If an install fails, show the error and ask (selection list): "What do you want to do?"
  Options: `["Retry", "Skip this plugin", "Stop installation"]`

After all installs complete, run via `Bash`:
```bash
claude plugin list
```
Show the updated list so the user can confirm everything is installed.

---

## Step 5: Completion

Ask (selection list): "What would you like to do next?"
Options:
- `"Install more plugins"` → go back to Step 1
- `"View installed plugins"` → run `claude plugin list` via `Bash` and show output
- `"Done"` → summarize what was installed and finish
