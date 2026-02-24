# Vault Plugin for Claude Code

A Zettelkasten-style vault manager for Obsidian. Process inbox, organize files,
research topics, and think with your notes.

## ⚠️ Warning: Beta Software

**Back up your vault before using this plugin.** Use git, Obsidian Sync, or
another backup solution. Claude can make mistakes and may corrupt or delete
files in your vault. This has happened. You have been warned.

## The Problem

You capture ideas, links, and thoughts in Obsidian, but they just sit there
disconnected. You forget what you've captured. You don't see the connections
between notes.

## The Solution

A Claude Code plugin that:

- **Processes** your inbox into atomic, linked notes
- **Discovers** connections between notes automatically
- **Organizes** your vault with consistent structure
- **Compacts** fragmented small notes into consolidated files
- **Researches** topics with competitors and market signals
- **Thinks** with your notes — traces ideas, finds patterns, challenges beliefs

## Installation

```bash
# Add the marketplace
/plugin marketplace add markphelps/claude-plugins

# Install the vault plugin
/plugin install vault@markphelps-marketplace
```

For local development:

```bash
# From local directory
/plugin install /path/to/vault-plugin

# Or load temporarily
claude --plugin-dir /path/to/vault-plugin
```

## Getting Started

After installing, navigate to your Obsidian vault and run:

```bash
/vault:init
```

This creates the required folder structure:

- `inbox/` - Where you'll drop ideas, links, and thoughts
- `daily/` - Daily notes for `/vault:review`
- `_templates/daily.md` - Daily note template (works with Obsidian's Daily Notes
  plugin)
- `._meta/plans/` - Where execution plans are saved for safety

The command is safe to run multiple times - it won't overwrite existing files.

## Commands

### Management

| Command           | Description                                     |
| ----------------- | ----------------------------------------------- |
| `/vault:init`     | Initialize vault folder structure and templates |
| `/vault:process`  | Process inbox items into linked notes           |
| `/vault:organize` | Reorganize vault files and update frontmatter   |
| `/vault:compact`  | Merge clusters of small related notes into one  |
| `/vault:cleanup`  | Delete old processed items and plan files       |

### Thinking

| Command                                | Description                                            |
| -------------------------------------- | ------------------------------------------------------ |
| `/vault:ideas`                         | Scan vault for patterns, generate ideas to build/write |
| `/vault:trace [topic]`                 | Track how an idea evolved over time                    |
| `/vault:connect [topic A] + [topic B]` | Find unexpected connections between two topics         |
| `/vault:drift`                         | Surface recurring themes you haven't noticed           |
| `/vault:challenge [topic]`             | Pressure-test your thinking, find contradictions       |
| `/vault:review`                        | Daily planning (morning) and reflection (evening)      |

### Research

| Command                  | Description                                        |
| ------------------------ | -------------------------------------------------- |
| `/vault:research [note]` | Research a note (competitors, discussions, market) |

## Quick Start

1. Run `/vault:init` to create the folder structure
2. Dump ideas, links, and thoughts into the inbox (any format)
3. Run `/vault:process` to turn them into linked notes
4. Run `/vault:organize` to clean up file structure
5. Run `/vault:compact` to merge fragmented small notes
6. Run `/vault:research my-note` to research a specific topic
7. Run `/vault:ideas` to see what patterns emerge from your notes

## How It Works

### Inbox Processing

`/vault:process` reads your inbox and creates notes:

- **URLs** become lightweight reference notes (title, description, link)
- **Substantial thoughts** (>280 chars) become new atomic notes
- **Short thoughts** get appended to related existing notes

After creating notes, it discovers connections and adds bidirectional links.

Example:

```
inbox/:
├── 2025-01-04.md (daily log with thoughts)
├── cool-article.md (just a URL)
└── shower-thought.md

AFTER PROCESSING:
inbox/
└── _processed/
    └── 2025-01-04/
        └── (archived originals)

notes/
├── voice-ui-trends.md (from URL, linked to voice-notes-app)
├── voice-notes-app.md (updated with new thought + backlinks)
└── notification-fatigue.md (new note from substantial thought)
```

### Automatic Linking

When processing or organizing, the plugin:

1. Extracts key concepts from each note
2. Searches for related notes (by filename, tags, content)
3. Adds `[[wikilinks]]` inline where natural
4. Adds discovered connections to a `## Related` section
5. Updates the target notes with backlinks

### Vault Organization

`/vault:organize` cleans up your file structure:

- Renames files/folders to consistent kebab-case
- Groups related files together
- Updates frontmatter on all notes
- Saves execution plans before making changes

Flags:

- `--dry-run` - Preview changes without executing
- `--yes` - Execute without confirmation
- `--no-move` - Only update frontmatter, don't reorganize
- `--shallow` - Only reorganize top-level

### Note Compaction

`/vault:compact` finds clusters of small, fragmented notes and merges them:

- **Date-series detection** — `health-tracker-2026-02-17.md`,
  `health-tracker-2026-02-18.md`, ... → single `health-tracker.md` with dated
  sections
- **Cross-folder awareness** — finds scattered files with the same base name
- **Tag-based clustering** — groups notes with high tag overlap

All content is preserved. Wikilinks are updated throughout the vault.

### Thinking With Your Notes

The thinking commands use your vault as a second brain:

- **`/vault:ideas`** — Scans for patterns across your notes and generates ideas
  for things to build, write, explore, or revisit
- **`/vault:trace AI agents`** — Shows a timeline of how your thinking on a
  topic evolved, with quotes from your notes
- **`/vault:connect filmmaking + AI`** — Finds unexpected bridges between two
  topics in your vault
- **`/vault:drift`** — Surfaces recurring themes that appear across unrelated
  notes — things you keep returning to without realizing it
- **`/vault:challenge "building in public"`** — Finds contradictions in your
  thinking, weak assumptions, and blind spots
- **`/vault:review`** — Morning planning or evening reflection. Creates daily
  notes with a consistent template

### Daily Notes

`/vault:review` creates and works with daily notes in `daily/`. Run
`/vault:init` to set up the template.

For Obsidian integration, enable the Daily Notes core plugin and set:

- **Template:** `_templates/daily`
- **Folder:** `daily`
- **Date format:** `YYYY-MM-DD`

### Frontmatter

The plugin uses minimal, flexible frontmatter:

```yaml
---
created: 2025-01-04
updated: 2025-01-04
tags: [topic, concept]
source: inbox | url | manual | compacted
status: active | someday | done | archived # optional, for projects
---
```

### Research

`/vault:research` searches for:

- Direct and adjacent competitors
- Reddit, HN, and forum discussions
- Market signals and trends

Findings are appended to your note with sources.

### Cleanup

`/vault:cleanup` removes old archived items:

- Processed inbox items older than 90 days
- Execution plan files older than 90 days

```bash
/vault:cleanup              # Default: 90 days
/vault:cleanup --days 30    # Custom threshold
/vault:cleanup --dry-run    # Preview only
```

## File Structure

Running `/vault:init` creates this structure:

```
vault/
├── inbox/
│   ├── (your raw captures)
│   └── _processed/
│       └── 2025-01-04/
├── daily/
│   └── 2025-01-04.md
├── _templates/
│   └── daily.md
├── (your organized notes)
└── ._meta/
    └── plans/
```

The `._meta/` folder stores execution plans for safety and auditability.

## Subagent Architecture

The plugin uses subagents to parallelize work and minimize costs:

| Task                    | Model  | Why                     |
| ----------------------- | ------ | ----------------------- |
| Content classification  | haiku  | Fast, cheap, sufficient |
| URL metadata extraction | haiku  | Simple web fetches      |
| Concept extraction      | haiku  | Basic NLP               |
| Link discovery          | sonnet | Nuanced matching        |
| Research synthesis      | sonnet | Complex reasoning       |

**Principle:** Use the smallest model that can do the job.

## Limitations

- Research quality depends on how well notes are described
- Web search results vary by topic
- No background processing - commands run manually
- Works on local vault only

## License

MIT

## Author

Mark Phelps
