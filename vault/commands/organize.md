---
description: Reorganize vault files - rename, group, and update frontmatter
---

# Organize Vault

Reorganize files in the vault - rename to consistent format, group related
files, and update frontmatter. Does not touch the inbox folder.

## Arguments

`$ARGUMENTS` - Path and optional flags.

**Path:** Can be relative or absolute. Resolves from current working directory.

- `/vault:organize notes` - Organize `./notes/` relative to cwd
- `/vault:organize ./projects` - Relative path
- `/vault:organize /Users/me/vault` - Absolute path
- `/vault:organize` - Defaults to current directory

**Flags:**

- `--dry-run` - Show plan only, don't execute
- `--yes` - Execute without confirmation
- `--no-move` - Update frontmatter only, don't reorganize files
- `--shallow` - Only reorganize top-level, don't touch subfolders

**Examples:**

```
/vault:organize notes              # Analyze, show plan, ask to execute
/vault:organize notes --dry-run    # Just show plan
/vault:organize notes --yes        # Execute without asking
/vault:organize notes --no-move    # Only update frontmatter
```

**Skips:**

- Hidden folders (`.git/`, `.obsidian/`, `._meta/`, etc.)
- Hidden files (`.DS_Store`, `.gitignore`, etc.)
- `inbox/` folder (use `/vault:process` for that)

## Process

### Step 1: Discover content

Use Glob and Bash (`ls`) to find:

- All `.md` files
- All subfolders and their contents (recursively by default, top-level if
  `--shallow`)
- Non-markdown files (images, CSVs, etc.)

Build a map of the current structure.

### Step 2: Analyze files (parallelized)

**For large vaults (10+ files), use subagents:**

Launch multiple `haiku` subagents in parallel:

```
Task(
  subagent_type: "general-purpose",
  model: "haiku",
  prompt: "Analyze these markdown files and return structured metadata...",
  run_in_background: true
)
```

Each subagent returns:

- File path
- Suggested folder name
- Suggested file name
- Inferred tags
- Current frontmatter state

**For small vaults (<10 files), analyze directly.**

For each markdown file:

1. Read the content
2. Extract existing frontmatter
3. Determine:
   - What is the core topic?
   - What tags apply?
   - What's a good filename?
   - Should it be grouped with other files?

### Step 3: Design new structure

Based on analysis, design an organized structure. Principles:

- **Group by topic** - Related files go together
- **Consistent naming** - lowercase, kebab-case
- **Clear file names** - Descriptive, not generic

Example transformation:

```
BEFORE:
├── Random Thoughts.md
├── carsearch/
│   └── 90-DAY MVP AI CAR SHOPPING AGENT.md
├── Voice Notes App idea.md
└── voice-notes/
    └── research.md

AFTER:
├── car-search/
│   └── prd.md
├── voice-notes-app/
│   ├── idea.md
│   └── research.md
└── random-thoughts.md
```

### Step 4: Build and save execution plan

Create the hidden directory if needed:

```bash
mkdir -p ._meta/plans
```

Build the plan:

```json
{
  "created_at": "2025-01-04T15:30:00Z",
  "target_path": "notes/",
  "options": {
    "shallow": false,
    "no_move": false
  },
  "operations": {
    "create_folders": ["voice-notes-app/"],
    "renames": [
      { "from": "carsearch/", "to": "car-search/" },
      { "from": "Random Thoughts.md", "to": "random-thoughts.md" }
    ],
    "moves": [
      { "from": "Voice Notes App idea.md", "to": "voice-notes-app/idea.md" },
      { "from": "voice-notes/research.md", "to": "voice-notes-app/research.md" }
    ],
    "frontmatter_updates": [
      {
        "file": "car-search/prd.md",
        "set": {
          "updated": "2025-01-04",
          "tags": ["automotive", "ai"],
          "source": "manual"
        }
      }
    ],
    "delete_empty_folders": ["voice-notes/", "carsearch/"]
  },
  "status": "pending"
}
```

Write to: `._meta/plans/YYYY-MM-DD-HHMMSS.json`

### Step 5: Present plan

Display in human-readable format:

```
Proposed reorganization:

RENAMES:
  carsearch/ → car-search/
  "Random Thoughts.md" → "random-thoughts.md"

MOVES:
  "Voice Notes App idea.md" → voice-notes-app/idea.md
  voice-notes/research.md → voice-notes-app/research.md

NEW FOLDERS:
  voice-notes-app/

FRONTMATTER UPDATES:
  5 files will have frontmatter added/updated

Plan saved: ._meta/plans/2025-01-04-153000.json
```

**If `--dry-run`:** Stop here.

**If default:** Ask "Execute these changes? [Y/n]"

**If `--yes`:** Proceed to execution.

### Step 6: Execute

Read the plan from disk and execute exactly what's specified:

1. Create new folders (`mkdir -p`)
2. Rename folders (do folders first)
3. Move/rename files
4. Update frontmatter on all markdown files
5. Delete empty folders
6. Update plan status to `"executed"`

### Step 7: Update frontmatter

For each note, ensure frontmatter has:

```yaml
---
updated: YYYY-MM-DD
tags: [inferred, tags]
source: manual
---
```

Preserve existing values where present. Only add `status` field if the note
appears to be a project/idea.

### Step 8: Report results

```
Reorganization complete!

Changes made:
- Renamed 2 folders
- Renamed 3 files
- Moved 2 files
- Updated 8 files with frontmatter

Plan: ._meta/plans/2025-01-04-153000.json (executed)
```

## Naming Conventions

- **Folders:** lowercase, kebab-case (`my-project-name/`)
- **Files:** lowercase, kebab-case (`my-file-name.md`)
- **System files:** Prefix with `_` (`_archive/`)

## Safety

- **Always confirm** before changes (unless `--yes`)
- **Save plans to disk** before executing
- **Skip inbox folder** entirely
- **Skip hidden folders** (`.git/`, `.obsidian/`, etc.)
- **Git-aware:** If in a git repo, suggest committing before reorganizing
