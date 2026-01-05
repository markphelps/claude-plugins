---
description: Initialize vault folder structure in the current project
---

# Initialize Vault

Set up the vault plugin folder structure in the current directory.

## Arguments

`$ARGUMENTS` - Optional path to vault root. Defaults to current working
directory.

**Examples:**

```
/vault:init                     # Initialize in current directory
/vault:init ./my-vault          # Initialize in specific folder
/vault:init ~/Documents/notes   # Absolute path
```

## Process

### Step 1: Confirm location

Tell the user where you'll create the vault structure:

```
Initializing vault in: /path/to/vault
```

### Step 2: Create folder structure

Create these folders if they don't exist:

```
vault/
├── inbox/           # Where raw captures go
└── ._meta/
    └── plans/       # Execution plans saved here
```

Use `mkdir -p` to create folders (safe if they already exist).

### Step 3: Create starter files (optional)

If `inbox/` was just created (was empty), create a welcome file:

**inbox/welcome.md:**

```markdown
# Welcome to your vault inbox

Drop ideas, links, and thoughts here. Then run `/vault:process` to turn them
into linked notes.

## Examples of what to capture

- URLs you want to remember
- Shower thoughts and random ideas
- Daily logs with `## Heading` separators
- Anything you don't want to forget

---

Delete this file once you've added your first real item.
```

### Step 4: Report results

```
✓ Vault initialized

Created:
  - inbox/
  - ._meta/plans/

Next steps:
  1. Add items to inbox/ (links, thoughts, daily logs)
  2. Run /vault:process to create linked notes
  3. Run /vault:organize to clean up file structure
```

If folders already existed, say so:

```
✓ Vault already initialized

Existing folders found:
  - inbox/ (5 items)
  - ._meta/plans/

Ready to use. Run /vault:process to process inbox items.
```

## Safety

- Never overwrite existing files
- Never delete anything
- Safe to run multiple times (idempotent)
