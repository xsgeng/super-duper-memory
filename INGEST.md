# INGEST.md - Source Ingestion Protocol

> **CRITICAL**: Workflow for adding sources to the knowledge wiki.
> For general wiki structure, see [[AGENTS.md]].

---

## Quick Reference

| Source Type | Target Directory | Filename Format |
|-------------|------------------|-----------------|
| `paper` | `raw/papers/` | `YYYY-MM-DD-title.pdf` |
| `article` | `raw/articles/` | `YYYY-MM-DD-title.md` |
| `book` | `raw/books/` | `YYYY-MM-DD-title.pdf` |
| `video` | `raw/assets/` | `kebab-case.ext` |
| `dataset` | `raw/assets/` | `kebab-case.ext` |

---

## The 7-Step Ingest Workflow

### Step 1: Read and Analyze Source

**Read the index.md**

**From `inbox/` directory:**

**For PDFs:**
- Use `pdf` skill to extract **precise text content**
- Examine document length (pages/word count)
- **Long documents (>20 pages or >10k words):** Delegate to sub-agents:
  - Create one sub-agent per section/chapter or page range
  - **OR** create one sub-agent per wiki page to create (source, entities, concepts)

**Extract metadata:**
- source_type, title, author, publisher, publication date
- Main thesis or key message

| Source Type | Description |
|-------------|-------------|
| `paper` | Academic papers, preprints, conference proceedings |
| `article` | Blog posts, news articles, newsletters |
| `book` | Books, book chapters, monographs |
| `video` | Video recordings, lectures, podcasts |
| `dataset` | Data collections, code repositories |

### Step 2: Create Source Page

**Location:** `wiki/sources/YYYY-MM-DD-source-title.md`

**Frontmatter:**
```yaml
---
type: source
title: "Source Title"
source_type: paper  # paper, article, book, video, dataset
url: "https://example.com/source"
raw_path: "raw/papers/source-file.pdf"
date_published: 2024-01-15
date_added: 2024-02-01
author: "Author Name"
publisher: "Publishing Venue"
tags: [tag1, tag2, tag3]
status: processed  # processed, pending, archived
---
```

**Content sections:**
- **Summary**: 2-4 sentences (thesis, findings, relevance)
- **Key Claims**: Bullet points with source citations
- **Key Quotes**: 3-5 verbatim quotes with page numbers

### Step 3: Identify Entities

**Delegate sub-agent for this step**

**Scan for:** People, organizations, locations, products, events

**For each entity:**
- Check `wiki/entities/` for existing page
- Create new page if needed; update existing with new source reference
- Link in source page: `[[entity-filename|Display Name]]`

**Entity Frontmatter:**
```yaml
---
type: entity
title: "Entity Name"
entity_type: person  # person, organization, location, product, event
created: 2024-01-10
updated: 2024-05-15
tags: [tag1, tag2]
aliases: ["Alt Name", "Abbreviation"]
---
```

### Step 4: Identify Concepts
**Read index.md:** Get an overview of the wiki repo

**Scan for:** Abstract concepts, theories, frameworks, methodologies

**For each concept:**
- Check `wiki/concepts/` for existing page
- Create new page if needed; update existing with new information and links
- Link in source page

**Concept Frontmatter:**
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

### Step 5: Create Relationships

**Cross-link pages:**
- Add entity ↔ entity links in entity pages
- Add concept ↔ concept links in concept pages
- Cross-reference related sources
- Use wiki-link format: `[[filename|Display Name]]`

**Wiki-Link Rules:**
- Filename only (no `.md`, no directory prefix)
- Kebab-case for filenames
- Pipe `|` for display text override

### Step 6: Update Index and Log

**Index (`wiki/index.md`):**
- Add source under appropriate category/tag
- Include brief description

**Log (`wiki/log.md`):**
```
[2024-02-01T14:30:00] INGEST: source-title
- Entities created: [entity-1, entity-2]
- Concepts created: [concept-1]
```
**Append only**

### Step 7: Organize Raw File and Verify

**Move from inbox to organized location:**
```bash
mv inbox/2024-01-15-source-title.pdf raw/papers/
```

**Update `raw_path` in source page frontmatter** to match new location.

**Verify:**
- [ ] All referenced pages exist. Create if not exists.
- [ ] No orphan pages
- [ ] `raw_path` matches actual file location

---

## Image Handling

1. **Store** images in `raw/assets/source-name/`
2. **Describe** the image content in source page (LLMs cannot view images)
3. **Transcribe** any text/data visible in the image
4. **Reference** using relative path: `![Alt text](../../raw/assets/source/figure.png)`

---

## Templates

- source.md.template
- entity.md.template
- concept.md.template
