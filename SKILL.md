---
name: obsidian-forge
description: Use when the user asks to archive a conversation or save a session into Obsidian. Creates a structured session note and extracts durable knowledge into the vault's Knowledge taxonomy.
version: 1.1.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [obsidian, note-taking, sessions, knowledge-base, archiving]
    related_skills: [obsidian, hermes-agent-skill-authoring]
---

# Obsidian Forge

## Overview

Use this skill when the user explicitly asks to save, archive, or persist a conversation/session into Obsidian.

The workflow has two outputs:

1. A **structured session summary** saved under `Obsidian Vault/sessions/default_agent/`
2. **Durable knowledge notes** extracted from that same session and stored under `Obsidian Vault/Knowledge/`

The goal is to preserve both:

- **Sessions** = what happened in this conversation
- **Knowledge** = reusable information worth keeping beyond the conversation

Always prefer the Obsidian skill for the file operations and use concrete vault paths, not variables.

## When to Use

Trigger only when the user clearly intends persistence, for example:

- "save this session"
- "archive this conversation"
- "store this in Obsidian"
- "remember this in the vault"
- equivalent wording that indicates the user wants the conversation persisted

If the user explicitly asks to "save this session into the vault" or similar, use **obsidian-forge** (not the generic Obsidian workflow) so the archive includes both the session note and any durable knowledge extraction.

Do **not** run this skill for ordinary chat or temporary notes.

### User preference shortcut

- For finance / research deep-dives that contain reusable takeaways, always create the session note **and** look for at least one durable knowledge note worth storing under `Knowledge/`.
- If the conversation is mostly ephemeral with no reusable insight, still save the session note but say clearly that no durable knowledge was extracted.

## Prerequisites

Before writing anything:

1. Resolve the Obsidian vault path.
   - Prefer the configured `OBSIDIAN_VAULT_PATH`.
   - If unavailable, use the documented fallback: `C:\Users\Tan19\Documents\Obsidian Vault`
2. Check whether `sessions/default_agent` and `Knowledge` already exist.
3. Inspect current Knowledge topic folders and their existing notes.

## Step 1 — Generate a Structured Session Summary

Create one markdown note that captures the conversation in a clean, skimmable format.

### Session note structure

```md
---
type: session
date: YYYY-MM-DD
title: <Short Title>
agent: default_agent
source: conversation
tags: [<optional, for graph visualization>]
---

# Session — YYYY-MM-DD — <Short Title>

## Summary
Concise overview of what happened and why it matters.

## Key Insights
- Non-obvious findings
- Patterns or generalizable learnings

## Decisions
- Explicit decisions made during the session

## Actions
- Actionable next steps

## Entities
- Key concepts, tools, frameworks, papers, APIs, tickers, people, places, files

## Topics Covered
- [[Knowledge/<Topic>/<Existing Note>]] — only if corresponding knowledge note exists

## Knowledge Extracted
- [[Concept Name]] — brief description (only if new knowledge was actually created/updated)

## Open Questions
- Unresolved points or future directions
```

### Topics Covered rules

- **Only add wikilinks** if the session topic has a corresponding existing knowledge note in `Knowledge/<topic>/`
- Do NOT link to folders — link to specific .md files only
- If no corresponding knowledge note exists, omit this section entirely

### Summary rules

- Keep the summary faithful to the actual conversation.
- Favor clarity over verbosity.
- If the session was mostly one task, summarize the task, result, and implications.
- Include only what matters for later retrieval.

## Step 2 — File Naming Convention

Name the session file using a summarized label and the session date:

`<summarized-label>_YYYY-MM-DD.md`

Rules:

- lowercase only
- use underscores between the label and the date
- keep the label concise and human-readable
- base the label on the dominant topic of the session
- avoid special characters
- if a file with the same name already exists, add a numeric suffix like `_2`, `_3`

Examples:

- `obsidian_session_archiving_2026-06-22.md`
- `llm_memory_notes_2026-06-22.md`

## Step 3 — Session Storage Path

Store the session note in:

`Obsidian Vault/sessions/default_agent/`

Do not store the session note anywhere else.

## Step 4 — Extract Durable Knowledge

From the same session, extract only information that is worth keeping long-term.

### Keep only knowledge that is:

- reusable beyond this single session
- not time-specific
- stable across contexts
- a concept, principle, method, or enduring insight
- likely to help future work or decision-making

### Exclude:

- temporary thoughts
- session-specific events
- task status
- one-off decisions
- short-lived plans
- conversational filler
- details that only matter to this one session

If something is useful but still session-specific, keep it only in the session note, not in Knowledge.

## Step 5 — Find the Right Knowledge Topic Folder

Place each knowledge item under the most relevant topic folder inside:

`Obsidian Vault/Knowledge/`

### Topic selection rules

1. First inspect existing topic folders.
2. Prefer the best existing folder if it is a strong semantic fit.
3. If the item does not fit any existing folder, create a new clean topic folder.
4. Do not force a concept into the wrong category just to avoid making a new folder.

### Good folder names

Use clean conceptual names such as:

- `AI`
- `Systems`
- `Coding`
- `Markets`
- `Mental_Models`
- `Research`

If you create a new folder, choose a concise conceptual name that will age well.

## Step 6 — Check for Existing Knowledge Before Creating

**Critical: Before creating any new knowledge note, you MUST check for existing notes.**

1. **List all existing knowledge notes** in the relevant topic folder.
2. **Read each potentially relevant note** to check if the concepts already exist.
3. **Compare the session's knowledge** against existing content:
   - If the concepts already exist in an existing note → **do NOT create a new note**, do NOT add wikilink to Knowledge Extracted
   - If the concepts are related but not fully covered → **update the existing note** with new insights, add wikilink to Knowledge Extracted
   - If the concepts are entirely new → **create a new note**, add wikilink to Knowledge Extracted

4. **Search across all Knowledge folders** if the topic is broad.

This prevents duplicate notes and keeps the knowledge base clean.

## Step 7 — Create or Update Knowledge Notes

Each durable knowledge item should usually become its own standalone note, or update an existing one.

### Knowledge note format

```md
---
type: knowledge
topic: <Topic>
tags: [<optional, for graph visualization>]
created: YYYY-MM-DD
updated: YYYY-MM-DD
source: <session file or brief source label>
---

# <Concept Name>

## Definition
Clear explanation of the concept.

## Key Ideas
- Core points
- Important nuances

## Applications
Where and when it is useful.

## Limitations
Edge cases, trade-offs, or constraints.

## Source Sessions
- [[session_name_YYYY-MM-DD]] — only if this session was used to create/update the note
```

### Knowledge note format (updating existing)

When updating an existing note, add new insights to the appropriate section:

```md
## Source Sessions
- [[existing_session_YYYY-MM-DD]]
- [[new_session_YYYY-MM-DD]] — added new insights from this session
```

### Knowledge extraction rules

- Merge similar concepts into an existing note when appropriate.
- Prefer updating an existing note over creating a duplicate.
- If the session reveals a better wording, refine the existing note.
- Keep notes atomic enough to be searchable and reusable.
- Use Obsidian wikilinks for related notes when helpful.

## Step 8 — Session Note — Knowledge Extracted Section

In the session note's "Knowledge Extracted" section:

- **ONLY add wikilinks** if new knowledge was actually created or the existing note was updated
- **DO NOT add wikilinks** if the concepts already existed in an existing note and nothing new was added
- This keeps the session note honest about what was actually contributed

## Step 9 — Organization Rules

- Keep session notes in the sessions archive only.
- Keep durable knowledge in the Knowledge taxonomy only.
- Avoid duplicating the same idea across multiple notes.
- If a concept already exists, update it instead of creating a near-duplicate.
- If a new topic folder is created, keep its name stable and conceptual.
- **Only add wikilinks to Knowledge Extracted** when new knowledge was actually created/updated
- **Link knowledge to source session** only if that session contributed to the note

## Step 10 — Output Requirements

After finishing the archive workflow, return:

- the session file path
- the list of knowledge files created or updated
- a brief summary of what was extracted

If nothing durable was found, say so clearly and still save the session note.

## Common Pitfalls

1. **Saving ephemeral content into Knowledge.**
   Keep temporary decisions, tasks, and one-off session details in the session note only.

2. **Using vague file names.**
   The session file name should reflect the dominant topic and date, not generic labels like `notes` or `summary`.

3. **Creating duplicate knowledge notes.**
   Always search existing notes first; update existing notes when the concept already exists.

4. **Forcing knowledge into the wrong folder.**
   Create a new topic folder when the existing taxonomy does not fit.

5. **Skipping verification.**
   Confirm the files were actually written where intended.

6. **Adding wikilinks when no new knowledge was created.**
   Only add to "Knowledge Extracted" if you actually created or updated a knowledge note.

7. **Adding redundant backlinks.**
   Do not add backlinks sections — they are auto-generated by Obsidian.

## Verification Checklist

- [ ] Session note saved under `Obsidian Vault/sessions/default_agent/`
- [ ] Session file name includes a short topic title and the date
- [ ] Session note includes "Topics Covered" (only with valid wikilinks to existing knowledge notes)
- [ ] Session note includes "Knowledge Extracted" section (only with valid wikilinks)
- [ ] Knowledge notes saved under `Obsidian Vault/Knowledge/`
- [ ] Knowledge notes include "Source Sessions" only if session contributed to the note
- [ ] Knowledge notes reflect durable, reusable information only
- [ ] Existing notes were checked before creating new ones
- [ ] No duplicate knowledge notes created
- [ ] No wikilinks added when knowledge already existed
- [ ] New topic folders created only when needed
- [ ] Final response includes file paths and a summary of extracted knowledge