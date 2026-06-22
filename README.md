# Obsidian Forge Skill

`obsidian-forge` is a Hermes skill for archiving conversations into Obsidian.
It creates a structured session note and extracts durable knowledge into the vault's Knowledge taxonomy.

## What it does

When triggered by a user request such as "save this session" or "archive this conversation", the skill:

1. Generates a structured session summary
2. Saves it under `Obsidian Vault/sessions/default_agent/`
3. Extracts durable, reusable knowledge from the same session
4. Places knowledge notes into the most relevant folder under `Obsidian Vault/Knowledge/`

## Use case

Use this skill when you want Hermes to preserve more than just the chat transcript.
It is designed to separate:

- **Sessions** — what happened in the conversation
- **Knowledge** — stable ideas worth reusing later

That makes the vault easier to search, browse, and maintain over time.

## Files in this repo

- `SKILL.md` — the actual Hermes skill definition
- `README.md` — human-readable overview and use case

## Storage rules

- Session notes go to `Obsidian Vault/sessions/default_agent/`
- Durable knowledge goes to `Obsidian Vault/Knowledge/`
- Existing knowledge notes should be updated when possible instead of duplicating content

## Naming convention

Session files use a topic-based slug plus date, for example:

`obsidian-session-archiving-2026-06-22.md`

## Notes

This repository is the single source of truth for the skill. Edits to this directory are pushed directly to GitHub.
