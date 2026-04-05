---
name: obsidian-check
description: Validate Obsidian backend connectivity and vault health. Run diagnostics on MCP server, headless CLI, vault path, sync status, and vault structure. Use when the user says /obsidian-check, check obsidian, obsidian status, vault health, or any variation of "is my vault working".
---

# Obsidian Check: Backend Health Diagnostics

## Overview

Run a series of diagnostic checks against the Obsidian backend and report results as a status table with actionable fix instructions.

**Announce at start:** "Running Obsidian backend diagnostics."

## Checks

Run all checks in order. Collect results, then present a single summary table.

### 1. Obsidian MCP

Call `mcp__obsidian__list_directory` with `path="sessions"`.

- **Pass:** MCP server responded and listed the sessions directory.
- **Fail:** MCP server did not respond or returned an error. Record the error message.

### 2. Headless CLI

Run `ob --version` via Bash.

- **Pass:** Command returned a version string. Record the version.
- **Fail:** Command not found or returned an error. This means obsidian-headless is not installed or Node.js 22+ is missing.

### 3. Vault Path

Check `OBSIDIAN_VAULT_PATH` environment variable first. If not set, check whether `~/.claude/vault/` exists as a directory.

- **Pass:** A vault directory was found. Record the resolved path.
- **Fail:** Neither the environment variable nor the default path resolves to a directory.

### 4. Sync Status

Run `ob sync-status --path <vault-path>` via Bash, using the vault path from Check 3. Skip this check if Check 2 or Check 3 failed.

- **Pass:** Sync is active and connected.
- **Fail:** Sync is inactive, disconnected, or the command returned an error. Record the status output.

### 5. Vault Structure

If any backend is available (Check 1 passed or Checks 2+3 passed), list the contents of `sessions/` using whichever backend succeeded.

- **obsidian backend:** `mcp__obsidian__list_directory` with `path="sessions"`
- **headless backend:** Bash `ls` on the vault sessions directory

Report the project folders found and total session file count.

- **Pass:** `sessions/` directory exists and contains at least one project folder.
- **Fail:** `sessions/` directory is missing or empty.

## Output

Present results as a markdown table:

```
## Obsidian Backend Diagnostics

| Check | Status | Details |
|---|---|---|
| Obsidian MCP | [pass/fail icon] | [details or error] |
| Headless CLI | [pass/fail icon] | [version or error] |
| Vault Path | [pass/fail icon] | [resolved path or not found] |
| Sync Status | [pass/fail icon] | [status or skipped] |
| Vault Structure | [pass/fail icon] | [N projects, M sessions or error] |

**Backend:** [obsidian / headless / none detected]
```

Use these status icons:
- Pass: `+`
- Fail: `-`
- Skipped: `~`

## Fix Instructions

After the table, provide actionable instructions for each failed check:

- **Obsidian MCP failed:** "Install Obsidian, enable the Local REST API community plugin, and connect an Obsidian MCP server to Claude Code. See README Tier 2."
- **Headless CLI failed:** "Install obsidian-headless: `npm i -g obsidian-headless`. Requires Node.js 22+."
- **Vault Path failed:** "Set `OBSIDIAN_VAULT_PATH` in your Claude Code environment config, or place your synced vault at `~/.claude/vault/`."
- **Sync Status failed:** "Run `ob login` and `ob sync-setup --vault <name>` to configure sync. Then start with `ob sync --continuous`."
- **Vault Structure failed:** "The `sessions/` directory is missing from your vault. Run `/session-log` to create your first session report, which will create the directory structure automatically."

If all checks pass, report: "All backend checks passed. Your Obsidian vault is healthy."

## Upgrade Path

After the fix instructions, show relevant upgrade suggestions based on the active backend:

- **Local vault active (no sync):** "Your sessions are stored locally at `~/.claude/vault/`. To sync across devices, install obsidian-headless (`npm i -g obsidian-headless`) and configure sync. See README Tier 1."
- **Headless active (no MCP):** "Sync is working. For full vault search and frontmatter management, add Obsidian MCP. See README Tier 2."
- **Obsidian MCP active:** No upgrade suggestion needed.
