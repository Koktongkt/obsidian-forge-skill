---
name: obsidian-forge
description: Use when the user asks to archive a conversation or save a session into Obsidian. Creates a structured session note and extracts durable knowledge into centralized Knowledge notes.
version: 1.2.0
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
2. **Durable knowledge** extracted from that same session and stored under centralized notes in `Obsidian Vault/Knowledge/`

The goal is to preserve both:

- **Sessions** = what happened in this conversation
- **Knowledge** = reusable information worth keeping beyond the conversation

The knowledge system should remain **centralized and queryable**. Prefer broad topic folders and reusable pillar/container markdown notes over many tiny atomic notes.

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

- For finance / research deep-dives that contain reusable takeaways, always create the session note **and** look for durable knowledge worth merging into the Knowledge taxonomy.
- If the conversation is mostly ephemeral with no reusable insight, still save the session note but say clearly that no durable knowledge was extracted.

## Prerequisites

Before writing anything:

1. Resolve the Obsidian vault path.
   - Prefer the configured `OBSIDIAN_VAULT_PATH`.
   - If unavailable, use the documented fallback: `C:\Users\Tan19\Documents\Obsidian Vault`
2. Check whether `sessions/default_agent` and `Knowledge` already exist.
3. Inspect current Knowledge topic folders and their existing notes.
4. Read likely relevant existing notes before creating or updating knowledge.

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
- [[<Existing Note>]] — brief description (only if corresponding knowledge note exists in Knowledge/<topic>/)

## Knowledge Extracted
- [[<Existing or New Knowledge Note>]] — what was created or updated

## Open Questions
- Unresolved points or future directions
```

### Topics Covered rules

- Add simplified wikilinks like `[[Stock Analysis Framework]]`, not full folder paths.
- Only add wikilinks if the session topic has a corresponding existing knowledge note in `Knowledge/<topic>/`.
- Do NOT link to folders.
- If no corresponding knowledge note exists, omit this section entirely.
- `Topics Covered` may link to existing notes even if the session did not update them.
- `Knowledge Extracted` should only link to notes that were created or updated.

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
- a concept, principle, framework, method, taxonomy, or enduring insight
- likely to help future work or decision-making

### Exclude:

- temporary thoughts
- session-specific events
- task status
- one-off decisions
- short-lived plans
- conversational filler
- details that only matter to this one session
- isolated facts that do not improve an existing reusable knowledge note

If something is useful but still session-specific, keep it only in the session note, not in Knowledge.

## Step 5 — Choose the Knowledge Container

Do not think only in terms of topic folders. First choose the broad topic folder, then choose the best **knowledge container note** inside it.

For expanded examples, the decision matrix, and wikilink conventions, see `references/knowledge-architecture.md`.

### Container-first decision order

1. Identify the broad top-level topic folder under `Obsidian Vault/Knowledge/`.
2. Inspect existing markdown notes inside that topic folder.
3. Prefer updating an existing broad / pillar / container note.
4. Add new knowledge as a section or subsection inside that note.
5. Create a new markdown note only when the concept deserves to be a reusable container.
6. Create a new topic folder only as a last resort.

### Topic folder rules

- Keep top-level topic folders broad and stable.
- Prefer existing folders such as `AI`, `Systems`, `Coding`, `Markets`, `Mental Models`, and `Research`.
- Do not create narrow folders for single entities, single sessions, one-off tools, or small subtopics.
- Create a new topic folder only when the concept clearly does not fit any current top-level folder and is likely to recur across future sessions.
- If in doubt, use the closest existing broad folder and create/update a better container note inside it.

### Pillar / container note examples

Prefer knowledge notes like Examples:

- `Stock Analysis Framework.md`
- `Crypto Asset Valuation.md`
- `AI Company Valuation.md`
- `Market Structure and Liquidity.md`
- `Agent Memory and Knowledge Systems.md`
- `Software Debugging Frameworks.md`

Avoid creating many atomic knowledge notes like:

- `Rule of 40.md` when it can be a section in `Stock Analysis Framework.md`
- `ETH Staking Yield.md` when it can be a section in `Crypto Asset Valuation.md`
- `Rollup Fee Leakage.md` when it can be a section in `Crypto Asset Valuation.md`
- `Bollinger Bands.md` when it can be a section in `Technical Analysis Framework.md`

## Step 6 — Check Existing Knowledge Before Creating

**Critical: Before creating any new knowledge note, you MUST check for existing notes and choose the least-fragmented home for the knowledge.**

1. **List existing knowledge notes** in the relevant topic folder.
2. **Search across all Knowledge folders** if the concept could belong to more than one area.
3. **Read each potentially relevant note** to check whether the concept already exists or fits as a section.
4. **Classify the session's durable knowledge:**
   - **Already covered:** the concept already exists in an existing note → do not update, do not create a new note, do not add a `Knowledge Extracted` wikilink.
   - **New detail for existing container:** the concept is related to an existing note but adds new insight → update that existing note, usually as a section/subsection/bullet, then add that note to `Knowledge Extracted`.
   - **New broad cluster:** the concept is broad, reusable, and cannot fit cleanly into an existing note → create a new knowledge note.
   - **Too narrow:** the concept is durable but too small to justify a note and does not improve an existing container → keep it in the session note only.

### New knowledge note creation threshold

Create a new knowledge note only if at least **two** of the following are true:

- It will likely be referenced in future sessions.
- It contains multiple sub-concepts or recurring questions.
- It does not fit cleanly inside an existing note.
- It represents a reusable framework, taxonomy, method, model, or domain.
- Adding it to an existing note would make that note confusing or too broad.
- It is a central entity or research object the user repeatedly studies.

A concept being new is **not** enough reason to create a new file.

## Step 7 — Create or Update Knowledge Notes

Durable knowledge should usually be merged into the most relevant existing knowledge note. Create a standalone note only when the concept is broad enough to serve as a reusable knowledge container.

### Section-first policy

- If extracted knowledge is a sub-concept, add it as a section or subsection inside an existing note.
- If extracted knowledge refines a framework, update the existing framework note.
- If extracted knowledge is an example or application, add it under `## Applications`, `## Examples`, or a relevant subsection.
- If extracted knowledge is a limitation, add it under `## Limitations`.
- Do not create one markdown file per small insight, metric, indicator, definition, entity, or observation.

### Knowledge note format for pillar/container notes

```md
---
type: knowledge
topic: <Topic>
scope: pillar
tags: [<optional, for graph visualization>]
created: YYYY-MM-DD
updated: YYYY-MM-DD
source: <session file or brief source label>
---

# <Broad Knowledge Container>

## Overview
High-level explanation of the knowledge area.

## Core Concepts

### <Subconcept A>
- Explanation, principles, and reusable details.

### <Subconcept B>
- Explanation, principles, and reusable details.

## Applications
Where and when this knowledge is useful.

## Limitations
Edge cases, trade-offs, or constraints.

## Related Notes (optional)
Only include durable semantic neighbors.

Include this section only when the connection is genuinely useful for long-term navigation or comparison.

Good reasons to include a related note:
- peer concept
- companion framework
- prerequisite concept
- contrasting concept
- stable extension of the same idea

Do not include:
- source sessions
- topics covered
- folders or parent categories
- transient references from the session
- notes that were only casually mentioned
- one-off examples or tangential mentions

Rules:
- Keep this section small and high-signal
- Usually 0–3 notes by default
- Use simplified wikilinks to note names only
- If the relationship is unclear or temporary, omit it

## Source Sessions
- [[session_name_YYYY-MM-DD]] — only if this session was used to create/update the note
```

### Updating existing notes

When updating an existing note:

1. Add new content to the most relevant existing section.
2. Create a new subsection only if it improves scanability.
3. Keep the note cohesive; if it becomes too broad, split only after a clear pattern emerges across multiple sessions.
4. Add or update `updated: YYYY-MM-DD` if frontmatter exists.
5. Add the session to `## Source Sessions` only if this session contributed new content to the note.

Example:

```md
## Source Sessions
- [[existing_session_YYYY-MM-DD]]
- [[new_session_YYYY-MM-DD]] — added section on valuation risk
```

### Knowledge extraction rules

- Prefer updating existing broad/pillar notes over creating new atomic notes.
- Merge similar concepts into an existing note when appropriate.
- If the session reveals a better wording, refine the existing note.
- Keep notes broad enough to be useful retrieval units but focused enough to stay coherent.
- Use Obsidian wikilinks for related existing notes when helpful, using simplified note-name links.

## Step 8 — Session Note — Knowledge Sections

In the session note:

### Topics Covered

- Link to existing knowledge notes that the session meaningfully discussed.
- Use simplified wikilinks such as `[[Stock Analysis Framework]]`.
- Include notes even if they were not updated.
- Do not link to folders.

### Knowledge Extracted

- Only add wikilinks if new knowledge was actually created or an existing note was updated.
- Do not add wikilinks if the concepts already existed and nothing new was added.
- Link to the container note that was updated, not to a non-existent atomic concept.
- If nothing was extracted, write: `No new durable knowledge was extracted.`

## Step 9 — Organization Rules

- Keep session notes in the sessions archive only.
- Keep durable knowledge in the Knowledge taxonomy only.
- Prefer centralized pillar/container notes over many atomic notes.
- Avoid duplicating the same idea across multiple notes.
- If a concept already exists, update it instead of creating a near-duplicate.
- If a concept is new but fits an existing container, update the container note.
- If a concept is too narrow to improve a container note, leave it in the session note only.
- If a new topic folder is created, keep its name stable and conceptual.
- Only add wikilinks to `Knowledge Extracted` when new knowledge was actually created/updated.
- Link knowledge to source session only if that session contributed new content to the note.
- Do not add manual `Backlinks` sections; Obsidian generates backlinks automatically.

## Step 10 — Output Requirements

After finishing the archive workflow, return:

- the session file path
- the list of knowledge files created or updated
- the existing knowledge notes linked under `Topics Covered`
- a brief summary of what was extracted or merged

If nothing durable was found, say so clearly and still save the session note.

## Common Pitfalls

1. **Creating atomic notes for every insight.**
   Do not create one file per small idea. Prefer adding sections to centralized knowledge notes.

2. **Saving ephemeral content into Knowledge.**
   Keep temporary decisions, tasks, and one-off session details in the session note only.

3. **Using vague file names.**
   The session file name should reflect the dominant topic and date, not generic labels like `notes` or `summary`.

4. **Creating duplicate knowledge notes.**
   Always search existing notes first; update existing notes when the concept already exists or fits an existing container.

5. **Creating narrow topic folders.**
   New top-level topic folders should be rare. Prefer broad folders and better container notes.

6. **Adding wikilinks when no new knowledge was created.**
   Only add to `Knowledge Extracted` if you actually created or updated a knowledge note.

7. **Linking to folders or full paths.**
   Use simplified note-name wikilinks like `[[Stock Analysis Framework]]`, not `[[Knowledge/Markets]]` or `[[Knowledge/Markets/Stock Analysis Framework]]`.

8. **Adding redundant backlinks.**
   Do not add backlinks sections — they are auto-generated by Obsidian.

## Verification Checklist

- [ ] Session note saved under `Obsidian Vault/sessions/default_agent/`
- [ ] Session file name includes a short topic title and the date
- [ ] Session note includes `Topics Covered` only with valid simplified wikilinks to existing knowledge notes
- [ ] Session note includes `Knowledge Extracted` only for notes created or updated
- [ ] Existing Knowledge notes were searched before creating new ones
- [ ] Existing container/pillar notes were preferred over new atomic notes
- [ ] No duplicate knowledge notes were created
- [ ] No new topic folder was created unless clearly necessary
- [ ] New knowledge note creation passed the threshold in Step 6
- [ ] Knowledge notes include `Source Sessions` only if session contributed new content
- [ ] Knowledge notes reflect durable, reusable information only
- [ ] Final response includes file paths and a summary of extracted or merged knowledge
