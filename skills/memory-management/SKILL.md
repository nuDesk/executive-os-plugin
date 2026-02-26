---
name: memory-management
description: >
  Two-tier memory system that makes Claude a workplace collaborator. Decodes shorthand,
  acronyms, nicknames, and internal language so Claude understands requests like a colleague
  would. Use when resolving unknown names, terms, or acronyms, or when the user asks to
  "remember this", "who is X", "what does X mean", or when updating memory after a session.
version: 1.0.0
---

# Memory Management

Memory makes Claude your workplace collaborator — someone who speaks your internal language.

## The Goal

Transform shorthand into understanding:

```
User: "follow up with Sarah about the SOW"
              -> Claude decodes
"Follow up with Sarah Chen (Acme Corp) about the
 Statement of Work (rescheduled from last Tuesday)"
```

## Architecture

```
~/.claude/CLAUDE.md          <- Hot cache (Working Memory section)
~/.claude/memory/
  glossary.md                <- Full decoder ring (everything)
  people/                    <- Complete profiles
  projects/                  <- Project details
  context/                   <- Company, teams, tools
```

**CLAUDE.md (Hot Cache):**
- Top ~10 people you interact with most
- ~10 most common acronyms/terms
- Active clients (~5)
- **Goal: Cover 90% of daily decoding needs**

**~/.claude/memory/glossary.md (Full Glossary):**
- Complete decoder ring — everyone, every term
- Searched when something isn't in CLAUDE.md
- Can grow indefinitely

**~/.claude/memory/people/, projects/, context/:**
- Rich detail when needed for execution
- Full profiles, history, context

## Lookup Flow

```
User mentions an unknown name/term/acronym

1. Check ~/.claude/CLAUDE.md Working Memory section
   -> Covers 90% of daily cases

2. If not found -> read ~/.claude/memory/glossary.md
   -> Full glossary has everyone/everything

3. If still not found -> check ~/.claude/memory/people/ or projects/
   -> Rich detail files

4. If still not found -> ask user
   -> "What does X mean? I'll remember it."
```

## Adding Memory

When user says "remember this" or new context emerges:

1. **Glossary items** (acronyms, terms, shorthand):
   - Add to `~/.claude/memory/glossary.md`
   - If frequently used, promote to CLAUDE.md Working Memory

2. **People:**
   - Create/update `~/.claude/memory/people/{name}.md`
   - Add to CLAUDE.md Working Memory People table if important
   - **Capture nicknames** — critical for decoding

3. **Projects:**
   - Create/update `~/.claude/memory/projects/{name}.md`
   - Add to CLAUDE.md if actively referenced

4. **Preferences:** Add to CLAUDE.md Preferences section

## Recalling Memory

When user asks "who is X" or "what does X mean":

1. Check CLAUDE.md Working Memory first
2. Check `~/.claude/memory/` for full detail
3. If not found: "I don't know what X means yet. Can you tell me?"

## Promotion / Demotion

**Promote to CLAUDE.md when:**
- A term/person is used frequently
- It's part of active work

**Demote to ~/.claude/memory/ only when:**
- Project completed
- Person no longer a frequent contact
- Term rarely used

This keeps CLAUDE.md fresh and relevant.

## Conventions

- **Bold** terms in CLAUDE.md tables for scannability
- Keep CLAUDE.md Working Memory compact (~30-40 lines)
- Filenames: lowercase, hyphens (`project-name.md`, `person-name.md`)
- Always capture nicknames and alternate names
- Use table format for glossary and working memory entries
- When something's used frequently, promote it to CLAUDE.md
- When something goes stale, demote it to `~/.claude/memory/` only
