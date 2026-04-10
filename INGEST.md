# INGEST.md - Source Ingestion Protocol

> **CRITICAL**: This document defines the complete workflow for adding new sources to the knowledge wiki.
> Agents MUST follow this protocol when ingesting new information.
>
> For general wiki structure and conventions, see [[AGENTS.md|Knowledge Wiki Protocol]].

---

## Overview

The ingest workflow is the standard 10-step process for adding new information to the wiki. This protocol ensures every source is properly captured, attributed, and connected to the existing knowledge graph.

**Inbox Pattern:** Files are initially placed in `inbox/`, then organized into `raw/` subdirectories after ingestion.

---

## The 10-Step Ingest Workflow

### Step 1: Acquire Raw Material

- Find the source document (PDF, article, etc.) in `inbox/`
- Preserve original filename when possible
- If no filename exists, create one using kebab-case with date prefix: `YYYY-MM-DD-source-title.ext`

**Inbox Location:** `inbox/YYYY-MM-DD-source-title.pdf`

---

### Step 2: Read and Analyze

- Read the entire document thoroughly. Use pdf skill for PDFs.
- Identify the source type (paper, article, video, etc.)
- Note the publication date, author, and publisher
- Extract the main thesis or key message

| Source Type | Description |
|-------------|-------------|
| `paper` | Academic papers, preprints, conference proceedings |
| `article` | Blog posts, news articles, newsletters |
| `book` | Books, book chapters, monographs |
| `video` | Video recordings, lectures, podcasts |
| `dataset` | Data collections, code repositories |

---

### Step 3: Create Source Page

Create a new file in `wiki/sources/` with kebab-case naming: `YYYY-MM-DD-source-title.md`

**Source Page Frontmatter:**
```yaml
---
type: source
title: "Source Title"
source_type: paper  # paper, article, book, video, dataset
url: "https://example.com/source"  # Original source URL
raw_path: "raw/papers/source-file.pdf"  # Target path after organization
date_published: 2024-01-15  # Original publication date
date_added: 2024-02-01  # When ingested (YYYY-MM-DD)
author: "Author Name"
publisher: "Publishing Venue"
tags: [tag1, tag2, tag3]
status: processed  # processed, pending, archived
---
```

**Note:** The `raw_path` field should reflect the **final location** in `raw/` after Step 11, not the inbox location.

**Summary Section:** Write a 2-4 sentence summary capturing:
- Main thesis or key message
- Key findings or contributions
- Relevance to the wiki's knowledge domain

---

### Step 4: Identify Entities

- Scan for all named entities (people, organizations, locations)
- For each unique entity, check if an entity page exists in `wiki/entities/`
- If new, create entity page; if existing, update with new source reference
- Link entities in the source page using wiki-links: `[[entity-filename|Display Name]]`

**Entity Frontmatter (for new entities):**
```yaml
---
type: entity
title: "Entity Name"
entity_type: person  # person, organization, location, product, event
created: 2024-01-10
updated: 2024-05-15
tags: [tag1, tag2]
aliases: ["Alternative Name", "Abbreviation"]
---
```

---

### Step 5: Identify Concepts

- Identify abstract concepts, theories, or frameworks discussed
- For each concept, check `wiki/concepts/` for existing pages
- If new, create concept page; if existing, update with new information
- Add concept links to the source page

**Concept Frontmatter (for new concepts):**
```yaml
---
type: concept
title: "Concept Name"
category: theory  # theory, methodology, framework, principle
created: 2024-02-01
updated: 2024-04-10
tags: [tag1, tag2, tag3]
aliases: ["Synonym", "Related Term"]
---
```

---

### Step 6: Create Relationships

- Add links between related entities in their entity pages
- Add links between related concepts in their concept pages
- Cross-reference the source page with related sources
- Use wiki-links: `[[target-filename|Display Name]]`

**Key Rules:**
- Use filename without `.md` extension
- No directory prefixes (NOT `[[sources/page-name]]`)
- Use kebab-case for filenames
- Pipe `|` separates filename from display text

---

### Step 7: Add Key Quotes

- Extract 3-5 verbatim quotes that are particularly insightful
- Include page numbers or timestamps when available
- Format as blockquotes with attribution

```markdown
> "Verbatim quote from the source."
> — Author Name, *Source Title*, p. 42
```

---

### Step 8: Update Index

- Add the new source to `wiki/index.md`
- List under appropriate category or tag
- Include brief description for discoverability
- Link to the source page and key entities

---

### Step 9: Log the Ingestion

- Add an entry to `wiki/log.md` with timestamp
- Note the source ingested and any notable actions taken
- Use Unix-parseable format for automated processing

---

### Step 10: Verify Connectivity

- Check that all internal links work
- Verify all referenced pages exist
- Ensure no orphan pages were created
- Run a quick lint check

```bash
# Find orphan pages (no incoming links)
grep -r "\[\[page-name\]" wiki/ --include="*.md" | wc -l

# Check for broken wiki-links
# (Parse all [[link]] patterns and verify files exist)
```

---

### Step 11: Organize Raw File

After successful ingestion, move the raw file from the inbox to its organized location:

1. Determine the target subdirectory based on `source_type`:
   - `paper` → `raw/papers/`
   - `article` → `raw/articles/`
   - `book` → `raw/books/`
   - `video` or `dataset` → `raw/assets/`

2. Move the file: `inbox/source-file.pdf` → `raw/papers/source-file.pdf`

3. Ensure the `raw_path` in the source page frontmatter matches the new location.

**Example:**
```bash
mv inbox/2024-01-15-attention-is-all-you-need.pdf raw/papers/
```

---

## Image Handling During Ingest

When sources contain images:

1. **Place** images in `raw/assets/source-name/`
2. **Describe** what the image shows in the source page
3. **Transcribe** any text or data visible in the image
4. **Reference** using relative paths: `![Alt text](../../raw/assets/source-name/figure-1.png)`

**Note:** LLMs cannot directly view images. Always provide text descriptions.

---

## Quick Reference

### File Naming
- Source pages: `YYYY-MM-DD-source-title.md`
- Entity/concept pages: `kebab-case-name.md`
- Raw files: kebab-case with optional date prefix
- All lowercase, no spaces, use hyphens

### Key Directories
```
inbox/           → Inbox (temporary, files start here)
raw/papers/      → Academic papers (organized)
raw/articles/    → Web articles (organized)
raw/books/       → Books and chapters (organized)
raw/assets/      → Images, diagrams, datasets (organized)
wiki/sources/    → Source metadata pages
wiki/entities/   → Named entities
wiki/concepts/   → Abstract concepts
```

---

## See Also

- [[AGENTS.md]] - Complete wiki operational manual (directory structure, naming conventions, guardrails)
- [[source.md.template]] - Source page template
- [[entity.md.template]] - Entity page template
- [[concept.md.template]] - Concept page template
- [[wiki/index.md]] - Wiki Index
- [[wiki/log.md]] - Wiki Log
