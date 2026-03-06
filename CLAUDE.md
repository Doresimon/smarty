# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code plugin marketplace (`Doresimon/smarty`) — a collection of plugins published via GitHub and installable with:

```bash
claude plugin marketplace add Doresimon/smarty
claude plugin install <plugin-name>@smarty
```

## Structure

```
.claude-plugin/marketplace.json   — Marketplace registry (lists all published plugins)
plugins/<name>/
  .claude-plugin/plugin.json      — Plugin manifest (name, version, description)
  commands/<name>.md              — Slash commands
  skills/<name>/SKILL.md          — Auto-activating skills
  agents/<name>.md                — Subagents (if any)
  hooks/hooks.json                — Event hooks (if any)
```

## Adding or Updating a Plugin

1. Create `plugins/<plugin-name>/` with the standard structure above.
2. Register it in `.claude-plugin/marketplace.json` under `"plugins"`.
3. Bump the version in `plugins/<plugin-name>/.claude-plugin/plugin.json` on every meaningful change.
4. Commit and push — the marketplace updates automatically from GitHub.

**marketplace.json must stay in sync** with the actual plugins in `plugins/`. When adding a new plugin, add its entry to `marketplace.json` at the same time.

## Plugin Authoring Conventions

- **Commands**: Written as instructions *for Claude*, not for the user. Claude executes `Bash` commands directly — never instruct the user to run commands themselves.
- **User interaction**: Always use `AskUserQuestion` with structured options (`["Yes", "No"]`, selection lists, numbered multi-select). Include `"Other / enter manually"` on all single-choice lists.
- **Bash tool**: Confirm before system-modifying operations (installs, config changes). Show output inline.
- **Naming**: kebab-case for all files, directories, command names, and skill names.
- **Allowed tools**: Declare minimal necessary tools in command frontmatter.

## Current Plugins

| Plugin | Purpose |
|--------|---------|
| `openclaw-toolkit` | Setup, config, credentials, and management skills for OpenClaw |
| `claude-toolkit` | Marketplace browser and plugin installer wizard |
