---
name: obsidian-forge
description: Use when the user asks to archive a conversation or save a session into Obsidian. Creates a structured session note and extracts durable knowledge into the vault's Knowledge taxonomy.
version: 1.0.0
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
3. Inspect current Knowledge topic folders so new knowledge lands in the right place.

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
- Link to topic folders where knowledge was stored, e.g. [[Knowledge/AI]]
- Link to topic folders where knowledge was stored, e.g. [[Knowledge/Systems]]

## Knowledge Extracted
- [[Concept Name 1]] — brief description of what was extracted
- [[Concept Name 2]] — brief description of what was extracted

## Open Questions
- Unresolved points or future directions

## Backlinks
- Notes that reference this session (auto-populated when others link here)
```

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

## Step 6 — Cross-Reference Before Creating

Before creating any new knowledge note:

1. **Search existing notes** in the Knowledge folder for related concepts.
2. **Check topic folders** for similar ideas that could be updated instead.
3. **Add wikilinks** to existing notes that should connect to the new knowledge.
4. **Create bidirectional links** — if Note A relates to Note B, add the link to both notes.

This ensures the graph grows organically and avoids duplicate notes.

## Step 7 — Create or Update Knowledge Notes

Each durable knowledge item should usually become its own standalone note.

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
- [[session_name_YYYY-MM-DD]] — session(s) this knowledge was extracted from
- Link back to the source session(s) for traceability

## Related Notes
- [[Related Concept A]] — why this connection exists
- [[Related Concept B]] — why this connection exists

## Backlinks
- Notes that reference this concept (auto-populated when others link here)
```

### Knowledge extraction rules

- Merge similar concepts into an existing note when appropriate.
- Prefer updating an existing note over creating a duplicate.
- If the session reveals a better wording, refine the existing note.
- Keep notes atomic enough to be searchable and reusable.
- Use Obsidian wikilinks for related notes when helpful.

## Step 8 — Organization Rules

- Keep session notes in the sessions archive only.
- Keep durable knowledge in the Knowledge taxonomy only.
- Avoid duplicating the same idea across multiple notes.
- If a concept already exists, update it instead of creating a near-duplicate.
- If a new topic folder is created, keep its name stable and conceptual.
- **Create bidirectional wikilinks** — if Note A links to Note B, ensure Note B also links back to Note A.
- **Link session notes to knowledge** — each session should link to the knowledge it extracted.
- **Link knowledge to source session** — each knowledge note should link back to its source session.

## Step 9 — Output Requirements

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
   Search first and update existing notes when the concept already exists.

4. **Forcing knowledge into the wrong folder.**
   Create a new topic folder when the existing taxonomy does not fit.

5. **Skipping verification.**
   Confirm the files were actually written where intended.

6. **Forgetting to add wikilinks.**
   Always link session notes to the knowledge they extracted, and knowledge notes back to their source sessions.

7. **One-way links only.**
   Ensure bidirectional links — if Note A links to Note B, Note B must also link to Note A.

8. **Missing cross-references.**
   Search for related concepts before creating new notes; update existing notes with links instead of creating duplicates.

## Verification Checklist

- [ ] Session note saved under `Obsidian Vault/sessions/default_agent/`
- [ ] Session file name includes a short topic title and the date
- [ ] Session note includes "Topics Covered" section linking to topic folders
- [ ] Session note includes "Knowledge Extracted" section linking to knowledge notes
- [ ] Knowledge notes saved under `Obsidian Vault/Knowledge/`
- [ ] Knowledge notes include "Source Sessions" linking back to the source session
- [ ] Knowledge notes include "Related Notes" with bidirectional links
- [ ] Knowledge notes reflect durable, reusable information only
- [ ] Existing notes were updated when overlap existed
- [ ] New topic folders were created only when needed
- [ ] Bidirectional links are in place between related notes
- [ ] Final response includes file paths and a summary of extracted knowledge
