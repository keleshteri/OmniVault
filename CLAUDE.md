# OmniVault — LLM Wiki Schema

This file is the operating manual for the LLM (Claude Code) that maintains this vault.
Read this file at the start of every session before doing anything else.

---

## Directory layout

```
OmniVault/
├── CLAUDE.md           ← you are here — schema and operating rules
├── raw/                ← IMMUTABLE source documents (never modify)
│   └── assets/         ← downloaded images and attachments
└── wiki/               ← LLM-maintained knowledge base (you write this)
    ├── entities/       ← people, orgs, places, products, tools
    ├── concepts/       ← ideas, frameworks, theories, methods
    ├── sources/        ← one summary page per ingested source
    ├── comparisons/    ← side-by-side analysis pages
    ├── queries/        ← filed answers to questions
    ├── overview.md     ← high-level synthesis of everything in the wiki
    ├── index.md        ← catalog of every wiki page with one-line summaries
    └── log.md          ← append-only activity record
```

**Rule:** Never modify files under `raw/`. Create and edit only files under `wiki/` and this file.

---

## Page formats

### Source page — `wiki/sources/<slug>.md`

```markdown
---
title: "<title>"
type: source
source_url: "<url or file path>"
ingested: YYYY-MM-DD
tags: [tag1, tag2]
---

## Summary
2–4 paragraph synthesis of the source.

## Key points
- bullet points of the most important claims or data

## Entities mentioned
Links to entity pages: [[entity-name]], [[entity-name]]

## Concepts mentioned
Links to concept pages: [[concept-name]], [[concept-name]]

## Contradictions / open questions
Note anything that conflicts with other wiki pages or raises questions.
```

### Entity page — `wiki/entities/<slug>.md`

```markdown
---
title: "<name>"
type: entity
entity_type: person | org | place | product | tool
source_count: N
tags: [tag1, tag2]
---

## Overview
1–2 paragraph description.

## Key facts
- fact with [[source]] citation

## Related entities
[[entity-name]] — relationship description

## Appearances
Sources where this entity is mentioned: [[source-slug]], [[source-slug]]
```

### Concept page — `wiki/concepts/<slug>.md`

```markdown
---
title: "<concept name>"
type: concept
source_count: N
tags: [tag1, tag2]
---

## Definition
Concise definition.

## How it works
Explanation, with examples if helpful.

## Where it appears
[[source-slug]] — context note

## Related concepts
[[concept-name]] — relationship

## Open questions
Unresolved tensions or gaps in the current understanding.
```

### Comparison page — `wiki/comparisons/<slug>.md`

```markdown
---
title: "<X vs Y>"
type: comparison
tags: [tag1, tag2]
---

## Summary
One paragraph on the key difference.

## Comparison table
| Dimension | X | Y |
|-----------|---|---|
| ...       |   |   |

## Sources
[[source-slug]]
```

### Query page — `wiki/queries/<slug>.md`

```markdown
---
title: "<question asked>"
type: query
asked: YYYY-MM-DD
tags: [tag1, tag2]
---

## Answer
Full synthesized answer with citations.

## Sources consulted
[[source-slug]], [[wiki-page]]

## Follow-up questions
- questions this answer raised
```

---

## index.md format

`wiki/index.md` is a flat catalog. Update it on every ingest or whenever a new page is created.
Format:

```markdown
# Wiki Index
Last updated: YYYY-MM-DD | N pages

## Sources
- [[source-slug]] — one-line summary (YYYY-MM-DD)

## Entities
- [[entity-slug]] — one-line description

## Concepts
- [[concept-slug]] — one-line definition

## Comparisons
- [[comparison-slug]] — one-line description

## Queries
- [[query-slug]] — one-line question (YYYY-MM-DD)
```

---

## log.md format

`wiki/log.md` is append-only. Prepend new entries at the top (most recent first).
Each entry header must follow this pattern exactly (enables grep):

```
## [YYYY-MM-DD] <operation> | <title>
```

Operations: `ingest`, `query`, `lint`, `update`, `create`

Example:

```markdown
## [2026-05-21] ingest | Article Title
- Source: raw/sources/article.md
- Pages created: [[source-slug]], [[entity-a]], [[concept-b]]
- Pages updated: [[overview]], [[index]]
- Notes: contradicts claim on [[other-page]] about X
```

---

## Workflows

### Ingest a new source

1. Read the source file in `raw/`
2. Discuss key takeaways with the user if needed
3. Create `wiki/sources/<slug>.md`
4. Create or update entity pages for every significant entity mentioned
5. Create or update concept pages for every significant concept mentioned
6. Update `wiki/overview.md` if the source meaningfully changes the synthesis
7. Add a new entry to `wiki/index.md`
8. Prepend an entry to `wiki/log.md`

A single source will typically touch 5–15 wiki pages. That is expected and correct.

### Answer a query

1. Read `wiki/index.md` to find relevant pages
2. Read the relevant pages
3. Synthesize an answer with `[[page]]` citations
4. Ask the user: "Should I file this as a query page?"
5. If yes: create `wiki/queries/<slug>.md` and add it to `wiki/index.md` and `wiki/log.md`

### Lint the wiki

Check for:
- Pages referenced with `[[link]]` that don't exist yet → create stubs or flag
- Pages with no inbound links (orphans) → add cross-references or flag
- Claims contradicted by newer sources → update and note the revision
- Concepts mentioned in multiple sources but lacking their own page → create
- Stale claims in `overview.md` → revise
- Gaps in coverage → suggest new sources to find

Report findings and proposed fixes before making changes.

---

## Cross-referencing rules

- Always use `[[wiki-link]]` syntax for internal links, never bare URLs
- Link generously — when an entity or concept appears in a page, link it
- Prefer linking to the most specific relevant page
- Never create a dead link intentionally; create a stub page instead

---

## Naming conventions

- File names: lowercase, hyphen-separated, no spaces — e.g. `neural-scaling-laws.md`
- Slugs should be stable — don't rename pages once created (links will break)
- Page titles (in frontmatter) can be full natural-language names

---

## Obsidian tips (for the human)

- **Graph view**: best way to see what's connected; orphan nodes mean missing links
- **Dataview plugin**: queries over frontmatter — e.g. all sources by tag, all entities
- **Obsidian Web Clipper**: browser extension to clip articles directly to `raw/`
- **Download attachments**: Settings → Hotkeys → bind "Download attachments" → Ctrl+Shift+D
- **Marp plugin**: render query/comparison pages as slide decks if needed
