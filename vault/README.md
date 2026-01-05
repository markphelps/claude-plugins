# Vault Plugin for Claude Code

A Zettelkasten-style vault manager for Obsidian. Process inbox items into linked
notes, organize files, and research topics.

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
- **Researches** topics with competitors and market signals

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
- `._meta/plans/` - Where execution plans are saved for safety

The command is safe to run multiple times - it won't overwrite existing files.

## Commands

| Command                  | Description                                        |
| ------------------------ | -------------------------------------------------- |
| `/vault:init`            | Initialize vault folder structure                  |
| `/vault:process`         | Process inbox items into linked notes              |
| `/vault:organize`        | Reorganize vault files and update frontmatter      |
| `/vault:research [note]` | Research a note (competitors, discussions, market) |
| `/vault:cleanup`         | Delete old processed items and plan files          |

## Quick Start

1. Run `/vault:init` to create the folder structure
2. Dump ideas, links, and thoughts into the inbox (any format)
3. Run `/vault:process` to turn them into linked notes
4. Run `/vault:organize` to clean up file structure
5. Run `/vault:research my-note` to research a specific topic

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

### Frontmatter

The plugin uses minimal, flexible frontmatter:

```yaml
---
updated: 2025-01-04
tags: [topic, concept]
source: inbox | url | manual
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
