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

## Skills

| Skill | Command | What It Does |
|-------|---------|-------------|
| **dream** | `/dream` | Consolidate auto memory files -- audit frontmatter, deduplicate facts, prune stale content, rebuild MEMORY.md index |
| **session-log** | `/session-log` | Write a structured session report to your Obsidian vault with frontmatter, emoji sections, and outcome-oriented formatting |
| **session-tidy** | `/session-tidy` | Audit session logs for format drift, stale statuses, orphaned next-steps, and generate project rollup summaries |

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

Writes a structured session report to your Obsidian vault via the Obsidian MCP server.

- Auto-detects project name and increments session number
- Frontmatter: `type`, `project`, `session`, `date`, `status`, `blocked`, `tags`
- Content: emoji-prefixed outcome sections, tables for structured data, no play-by-play
- Falls back to conversation output if Obsidian MCP is unavailable

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

## Prerequisites

- Claude Code 1.0.33+
- For `/session-log` and `/session-tidy`: an [Obsidian MCP server](https://github.com/MarkusPfworlds/obsidian-mcp) connected to Claude Code

## Plugin Structure

```
claude-journal/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── skills/
│   ├── dream/
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
