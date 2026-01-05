---
name: vault-context
description:
  Use when the user asks about their notes, wants to brainstorm, or references
  their vault. Provides context about existing notes to inform responses.
---

# Vault Context Skill

When the user asks about their notes, mentions brainstorming, or wants to work
on a topic, first gather context about their existing vault.

## When to Use

- User asks "what notes do I have about X?"
- User says "I want to brainstorm" or "I have a new idea"
- User references "my notes" or "my vault"
- User asks for help developing or researching a topic
- User wants to compare notes or find connections

## Process

### 1. Check for existing notes

Search the vault for relevant notes:

- Use Glob to find `.md` files
- Use Grep to search for related concepts
- Check for notes with matching tags

### 2. Provide relevant context

When responding to the user:

- Reference related existing notes they have
- Note if a new topic connects to something they've captured before
- Suggest connections between notes
- Mention the current status of relevant notes (if `status` field is set)

### 3. Suggest actions

Based on context, suggest relevant commands:

- "You have notes on similar topics - want me to run `/vault:process` to find
  connections?"
- "This note hasn't been researched - want to run `/vault:research`?"
- "Your vault could use organizing - consider `/vault:organize`"

## Example Responses

**User:** "I have an idea for an AI-powered recipe app"

**With context:** "Interesting! I see you already have a few food/cooking
related notes - `meal-planning.md` and `grocery-list-optimizer.md`. This new
idea might connect well. Want me to add it to your inbox, or create a note
directly?"

**User:** "What should I work on next?"

**With context:** "Looking at your notes with `status: active`, you have
`voice-notes-app` (has research) and `api-monitoring` (no research yet). The
voice notes app is further along. Want me to research the API monitoring idea,
or help you plan the voice notes app?"

## Don't

- Don't overwhelm the user with their entire note list
- Don't reference notes that aren't relevant to the conversation
- Don't assume the user remembers every note they've captured
- Don't push commands if the user just wants to chat
