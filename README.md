<p align="center">
  <h1 align="center">claude-journal</h1>
</p>

<p align="center">
  Memory and session management for Claude Code.
</p>

<p align="center">
  <a href="https://github.com/jl-cmd/claude-journal/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License" /></a>
  <a href="https://code.claude.com/docs/en/plugins"><img src="https://img.shields.io/badge/Claude_Code-plugin-blueviolet" alt="Claude Code Plugin" /></a>
</p>

---

> **Works out of the box.** Sessions are stored in a local vault at `~/.claude/vault/` with zero setup. Add [obsidian-headless](https://github.com/obsidianmd/obsidian-headless) for cross-device sync, or Obsidian MCP for the full experience. Run `/obsidian-check` to see your current tier and upgrade options.

## Skills

| Skill | Command | What It Does |
|-------|---------|-------------|
| **dream** | `/dream` | Consolidate auto memory files -- audit frontmatter, deduplicate facts, prune stale content, rebuild MEMORY.md index |
| **session-log** | `/session-log` | Write a structured session report to your Obsidian vault with frontmatter, emoji sections, and outcome-oriented formatting |
| **session-tidy** | `/session-tidy` | Audit session logs for format drift, stale statuses, orphaned next-steps, and generate project rollup summaries |
| **obsidian-check** | `/obsidian-check` | Validate Obsidian backend connectivity and vault health -- diagnose MCP, headless CLI, vault path, sync, and structure |

## Install

```
/plugin marketplace add jl-cmd/claude-journal
/plugin install journal@claude-journal
```

Or locally:

```bash
git clone https://github.com/jl-cmd/claude-journal.git
claude --plugin-dir ./claude-journal
```

## `/dream` -- Memory Consolidation

The `/memory` UI shows `/dream` but the command doesn't exist yet ([#39135](https://github.com/anthropics/claude-code/issues/39135)). This skill fills the gap.

**4-phase process:** Audit > Propose > Execute > Verify

| Check | What It Does |
|-------|-------------|
| MEMORY.md is an index | No inline content, tables, or multi-line facts |
| Topic files need frontmatter | `name`, `description`, `type` fields present |
| Valid types only | `user`, `feedback`, `project`, `reference` |
| Semantic naming | Session-dated files flagged for rename |
| Under 200 lines | MEMORY.md line count check |
| No duplicates | Facts appearing in multiple files flagged |
| Stale content | TODOs/Next sections older than 14 days flagged |

No changes without your approval.

## `/session-log` -- Write Session Reports

Writes structured session reports with automatic backend detection. Works out of the box with zero setup.

- Auto-detects project name and increments session number
- Frontmatter: `type`, `project`, `session`, `date`, `status`, `blocked`, `tags`
- Content: emoji-prefixed outcome sections, tables for structured data, no play-by-play
- Automatically detects which Obsidian backend is available (see Setup Tiers below)

## `/session-tidy` -- Session Log Consolidation

The maintenance counterpart to `/session-log`. Run periodically or after finishing a multi-session project.

**4-phase process:** Audit > Propose > Execute > Verify

| Check | What It Does |
|-------|-------------|
| Naming convention | Files must be `[Project] Session [N].md` |
| Frontmatter compliance | All required fields present and correct type |
| Status coherence | Catches `completed` + `blocked: true` contradictions |
| Stale statuses | `in-progress` or `blocked` sessions older than 7 days |
| Orphaned next-steps | Cross-references queued items against later sessions |
| Project rollups | Generates summary notes for projects with 3+ sessions |

No changes without your approval.

## Setup Tiers

`/session-log` auto-detects your backend. Detection order: headless sync, Obsidian MCP, local vault.

### Tier 1: Zero Setup (local vault)

Works immediately after install. Sessions are stored as markdown files in `~/.claude/vault/sessions/`. The directory is created automatically on first use.

- No Obsidian required
- No subscription required
- Works in cloud environments, containers, and CI/CD
- Vault context tracking and session tidy use Bash alternatives (reduced capability)
- Upgrade to Tier 2 anytime to add sync

### Tier 2: Headless Vault (cross-device sync)

Sync your vault across devices using the official [obsidian-headless](https://github.com/obsidianmd/obsidian-headless) CLI. Your existing local vault sessions carry over.

```bash
npm i -g obsidian-headless
ob login
ob sync-setup --vault "SessionLog"
ob sync --continuous
```

Verify sync is active:

```bash
ob sync-status --path /path/to/synced/vault
```

Set the vault path so session-log can find it:

```json
{
  "env": {
    "OBSIDIAN_VAULT_PATH": "/path/to/synced/vault"
  }
}
```

Or place the vault at `~/.claude/vault/` (auto-detected).

Optionally add a filesystem MCP server like [StevenStavrakis/obsidian-mcp](https://github.com/StevenStavrakis/obsidian-mcp) for search and indexing without Obsidian desktop:

```bash
pip install obsidian-mcp
```

- Requires [Obsidian Sync](https://obsidian.md/sync) ($4/mo) for `obsidian-headless`
- Requires Node.js 22+ for `obsidian-headless`
- Vault context tracking and session tidy use Bash alternatives (reduced capability)

### Tier 3: Full Obsidian (desktop environments)

The complete experience with search, frontmatter management, vault context tracking, and session tidy.

1. Install [Obsidian](https://obsidian.md/)
2. Install the [Local REST API](https://github.com/coddingtonbear/obsidian-local-rest-api) community plugin
3. Connect an [Obsidian MCP server](https://github.com/MarkusPfundstein/mcp-obsidian) to Claude Code

All 5 steps of the session-log workflow run in this tier.

Run `/obsidian-check` to verify your backend and see upgrade options.

## Prerequisites

- Claude Code 1.0.33+

## Plugin Structure

```
claude-journal/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── skills/
│   ├── dream/
│   │   └── SKILL.md
│   ├── obsidian-check/
│   │   └── SKILL.md
│   ├── session-log/
│   │   └── SKILL.md
│   └── session-tidy/
│       └── SKILL.md
├── README.md
├── LICENSE
└── .gitignore
```

### Sources

- **Claude Code auto memory system prompt** -- the format contract injected into every session, defining memory types, MEMORY.md index structure, frontmatter requirements, and organization rules. This is the client system prompt itself, not a public docs page.
- [Plugin creation docs](https://code.claude.com/docs/en/plugins)
- [Skills authoring docs](https://code.claude.com/docs/en/skills)

## License

[MIT](LICENSE)
