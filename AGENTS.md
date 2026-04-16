# AGENTS.md - Knowledge Wiki Protocol

> **CRITICAL**: This file defines the operational schema for OpenCode's knowledge wiki system.
> Agents MUST read and follow this protocol when working with the wiki.

---

## Section 1: Overview

### Purpose

This document defines the complete operational protocol for managing a research-oriented knowledge wiki. The wiki serves as a structured repository for capturing, organizing, and synthesizing information from various sources, enabling AI agents to build cumulative knowledge and provide contextual, evidence-based responses.

### What This Wiki Is For

The knowledge wiki is designed for:

- **Research Projects**: Collecting and organizing findings from academic papers, technical documentation, and primary sources
- **Knowledge Synthesis**: Building connections between disparate pieces of information to generate novel insights
- **Evidence-Based Reasoning**: Maintaining clear provenance trails from claims back to their source materials
- **Cumulative Learning**: Enabling agents to build upon previous work rather than starting from scratch each session

### How OpenCode Operates the Wiki

OpenCode agents interact with this wiki through a standardized workflow:

1. **Ingestion**: When new information is encountered (documents, web pages, codebases), agents create source pages that capture the raw material with proper attribution
2. **Extraction**: Key entities, concepts, and claims are extracted from sources and given their own dedicated pages
3. **Synthesis**: Connections between entities and concepts are documented, creating a web of relationships
4. **Querying**: When answering questions, agents search the wiki, read relevant pages, and synthesize responses based on accumulated knowledge
5. **Maintenance**: Agents periodically run lint checks to ensure the wiki remains consistent and well-connected

The wiki operates on a "write once, reference often" principle. Source pages are immutable once created, while entity and concept pages evolve as new information is discovered. This creates a stable foundation of facts upon which dynamic understanding can be built.

### Core Principles

- **Provenance First**: Every claim must be traceable to its source
- **Atomic Information**: Each page represents a single, discrete unit of knowledge
- **Rich Linking**: Pages are heavily cross-referenced to enable navigation and discovery
- **Machine-Readable**: Structure and metadata enable automated processing and validation

### Quick Linking Reference

**Wiki-Link Format:**
```markdown
[[page-filename]]                         # Basic link
[[page-filename|Display Name]]           # Link with custom text
[[entity-001|Dr. Sarah Chen]]            # Entity link example
[[concept-001|Emergent Structures]]      # Concept link example
```

**Key Rules:**
- Use filename without `.md` extension
- No directory prefixes (e.g., NOT `[[sources/page-name]]`)
- Use kebab-case for filenames (e.g., `[[example-research-intro]]`)
- Pipe `|` separates filename from display text

---

## Section 2: Directory Structure

### Complete Tree Diagram

```
wiki-root/
├── raw/                          # Raw source materials (immutable)
│   ├── papers/                   # Academic papers and preprints
│   ├── articles/                 # Blog posts, news articles, essays
│   ├── books/                    # Book excerpts and chapters
│   └── assets/                   # Downloaded images, diagrams, and media files
└── wiki/                         # Processed knowledge pages
    ├── sources/                  # Source metadata pages (index into raw/)
    ├── entities/                 # Named entities (people, organizations, places)
    ├── concepts/                 # Abstract concepts and theories
    ├── synthesis/                # Synthesized analysis and insights
    ├── log.md                    # Chronological records 
    └── index.md                  # Master index of all wiki content
```

### Raw/ Subdirectories

**raw/papers/**: Store academic papers, research articles, and preprints. Files should maintain their original names when possible. Supported formats: PDF, TXT, Markdown.

**raw/articles/**: Store web articles, blog posts, newsletters, and long-form journalism. Capture the full content with original URL preserved in metadata.

**raw/assets/**: Store downloaded images, diagrams, figures, and other media files. Organize by source for easy reference.

### Wiki/ Subdirectories

**wiki/sources/**: Metadata pages that index into raw/ materials. Each source page contains summary, key claims, and entities mentioned.

**wiki/entities/**: Pages for named entities (people, organizations, locations, products). Entity pages aggregate all mentions across sources.

**wiki/concepts/**: Pages for abstract concepts, theories, methodologies, and frameworks. These explain ideas independent of sources.

**wiki/synthesis/**: Higher-order analysis combining multiple sources, entities, and concepts into new insights. Sources serve as citation units for claims.

### Special Navigation Files

Two files in the wiki root serve special navigation purposes:

**wiki/index.md** - Content-oriented catalog. Master index of everything in the wiki. See `index.md.template` for the full template and documentation.

**wiki/log.md** - Chronological record. Append-only log of all operations with Unix-parseable format. See `log.md.template` for the full template and documentation.

---

## Section 3: Page Templates

All page templates are stored as separate `.template` files:

- **Source pages** - Document the origin of information. See `source.md.template`
- **Entity pages** - Aggregate information about people, organizations, or things. See `entity.md.template`
- **Concept pages** - Explain abstract ideas, theories, and frameworks. See `concept.md.template`
- **Synthesis pages** - Combine multiple sources to generate new insights. See `synthesis.md.template`

---

## Section 4: Frontmatter Schema

All wiki pages use YAML frontmatter for metadata. This section documents the complete schema.

### Common Fields (All Pages)

These fields appear in every wiki page:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | string | Yes | Page type: `source`, `entity`, `concept`, `synthesis` |
| `title` | string | Yes | Display title for the page |
| `tags` | array | No | List of topical tags for categorization |

**Date Fields by Page Type:**
- **Source pages**: Use `date_added` (when ingested) and `date_published` (original date)
- **Entity/Concept/Synthesis pages**: Use `created` (page creation) and `updated` (last modification)

Example common frontmatter:
```yaml
---
type: concept
title: "Quantum Computing"
created: 2024-01-15
updated: 2024-03-22
tags: [physics, computing, quantum]
---
```

### Source-Specific Fields

Additional fields for source pages:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `source_type` | enum | Yes | Type: `paper`, `article`, `book`, `video`, `dataset` |
| `url` | string | No | Original source URL if available |
| `raw_path` | string | Yes | Relative path to raw file in raw/ directory |
| `date_published` | date | No | Original publication date |
| `date_added` | date | Yes | When ingested into wiki |
| `author` | string | No | Primary author(s) |
| `publisher` | string | No | Publishing organization or venue |
| `status` | enum | Yes | `processed`, `pending`, or `archived` |

Example source frontmatter:
```yaml
---
type: source
title: "Attention Is All You Need"
source_type: paper
url: "https://arxiv.org/abs/1706.03762"
raw_path: "raw/papers/attention-is-all-you-need.pdf"
date_published: 2017-06-12
date_added: 2024-02-01
author: "Ashish Vaswani et al."
publisher: "arXiv"
tags: [nlp, transformers, deep-learning]
status: processed
---
```

### Entity-Specific Fields

Additional fields for entity pages:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `entity_type` | enum | Yes | Type: `person`, `organization`, `location`, `product`, `event` |
| `aliases` | array | No | Alternative names or abbreviations |

Example entity frontmatter:
```yaml
---
type: entity
title: "Google DeepMind"
entity_type: organization
created: 2024-01-10
updated: 2024-05-15
tags: [ai, research, company]
aliases: ["DeepMind", "Google AI"]
---
```

### Concept-Specific Fields

Additional fields for concept pages:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `category` | enum | Yes | Type: `theory`, `methodology`, `framework`, `principle` |
| `aliases` | array | No | Synonyms or related terms |

Example concept frontmatter:
```yaml
---
type: concept
title: "Transformer Architecture"
category: framework
created: 2024-02-01
updated: 2024-04-10
tags: [deep-learning, nlp, architecture]
aliases: ["Attention Mechanism", "Self-Attention"]
---
```

### Synthesis-Specific Fields

Additional fields for synthesis pages:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `scope` | enum | Yes | Type: `topic`, `question`, `comparison` |
| `status` | enum | Yes | `draft`, `review`, or `complete` |

Example synthesis frontmatter:
```yaml
---
type: synthesis
title: "Evolution of LLM Architectures"
scope: topic
status: draft
created: 2024-03-01
updated: 2024-04-05
tags: [llm, architecture, history]
---
```

### Field Constraints

- **Dates**: Always use ISO 8601 format (YYYY-MM-DD)
- **Arrays**: Use YAML array syntax with square brackets or dashes
- **Strings**: Quote strings containing special characters or colons
- **Enums**: Use only the specified values for enum fields
- **Paths**: Use forward slashes for cross-platform compatibility

---

## Section 5: Ingest Workflow

The ingest workflow is the standard process for adding new information to the wiki. Follow these steps when encountering new sources.

### Step 1: Acquire Raw Material
- Download the source document (PDF, article, etc.)
- Store it in the appropriate raw/ subdirectory based on type
- Preserve original filename when possible
- If no filename exists, create one using kebab-case with date prefix

### Step 2: Read and Analyze
- Read the entire document thoroughly
- Identify the source type (paper, article, video, etc.)
- Note the publication date, author, and publisher
- Extract the main thesis or key message

### Step 3: Create Source Page
- Create a new file in wiki/sources/ directory
- Use kebab-case naming: `YYYY-MM-DD-source-title.md`
- Add complete frontmatter with all source-specific fields
- Write a 2-4 sentence summary of the source

### Step 4: Identify Entities
- Scan for all named entities (people, organizations, locations)
- For each unique entity, check if an entity page exists
- If new, create a page in wiki/entities/ with basic details
- If existing, update the entity page with new source reference
- Link entities in the source page to their entity pages

### Step 6: Identify Concepts
- Identify abstract concepts, theories, or frameworks discussed
- For each concept, check wiki/concepts/ for existing pages
- If new, create a concept page with definition and explanation
- If existing, update with any new information or sources
- Add concept links to the source page

### Step 7: Create Relationships
- Add links between related entities in their entity pages
- Add links between related concepts in their concept pages
- Cross-reference the source page with related sources
- Use wiki-links: `[[target-filename|Display Name]]`

### Step 8: Add Key Quotes
- Extract 3-5 verbatim quotes that are particularly insightful
- Include page numbers or timestamps when available
- Format as blockquotes with attribution
- These provide direct evidence from the source

### Step 9: Update Index
- Add the new source to index.md
- List under appropriate category or tag
- Include brief description for discoverability
- Link to the source page and key entities

### Step 10: Verify Connectivity
- Check that all internal links work
- Verify all referenced pages exist
- Ensure no orphan pages were created
- Run a quick lint check (see Section 8)

---

## Section 6: Query Workflow

The query workflow enables agents to answer questions using the accumulated wiki knowledge.

### Step 1: Search Index
- Open index.md and scan for relevant tags or categories
- Look for sources related to the query topic
- Note any entities or concepts that might be relevant
- Use grep to find mentions of key terms across wiki pages

### Step 2: Read Source Pages
- Identify sources that likely contain relevant information
- Read source pages to understand context and claims
- Extract specific claims related to the query
- Note any contradictions or gaps in information

### Step 3: Read Entity/Concept Pages
- Look up any entities mentioned in the sources
- Read concept pages to understand theoretical frameworks
- Check for related concepts that might provide context
- Build a complete picture of the knowledge landscape

### Step 4: Synthesize Answer
- Combine information from multiple sources
- Resolve any contradictions by noting conflicting evidence
- Formulate a clear, evidence-based answer
- Include citations to specific wiki pages and sources

### Step 5: File New Knowledge
- If the synthesis reveals new insights, create a synthesis page
- Link to all sources consulted
- Note any open questions or areas needing further research
- Update index.md with the new synthesis

---

## Section 7: Image Handling Protocol

### Storage Location

All images must be stored in the `raw/assets/` directory. Do not embed base64 images directly in wiki pages. This keeps the wiki text-based, searchable, and version-control friendly.

### Directory Organization

Organize images by source for easy reference:
```
raw/assets/
├── source-name-1/
│   ├── figure-1.png
│   └── diagram-2.jpg
├── source-name-2/
│   └── chart-1.png
└── uncategorized/
    └── misc-image.png
```

### Referencing Images

Reference images from wiki pages using relative paths:
```markdown
![Alt text](../../raw/assets/source-name/figure-1.png)
```

### LLM Limitation Note

LLMs cannot directly view or analyze images. When ingesting sources with important images:
1. Download the image to raw/assets/
2. In the source page, describe what the image shows
3. Include any text or data visible in the image as transcribed content
4. Note the figure number and caption from the original source

### Download Workflow

1. Identify images in the source material
2. Download using curl or wget with descriptive filename
3. Store in raw/assets/ under appropriate subdirectory
4. Reference in source page with description
5. Update source page frontmatter if image is critical to understanding

---

## Section 8: Lint Checks

Periodic linting ensures the wiki remains consistent and well-connected.

### Find Orphan Pages

Orphan pages are wiki pages that have no incoming links from other pages.
- These may indicate forgotten or unused content
- Run lint to list all pages with zero backlinks
- Either integrate orphans into the wiki structure or archive them
- Command: Search for files in wiki/ not referenced by any other file

### Find Missing Concepts

Check for concepts referenced but not defined:
- Scan wiki-links to concept pages that don't exist
- These indicate gaps in the knowledge base
- Create concept pages for frequently referenced undefined terms
- Command: Find wiki-links with no corresponding file

### Find Stale Sources

Identify sources that may be outdated:
- Sources marked as archived but still referenced
- Contradictory information from different time periods
- Command: List sources by date and status

### How to Run Lint

The lint process can be run manually:
```bash
# Find orphan pages (no incoming links)
grep -r "\[\[page-name\]" wiki/ --include="*.md" | wc -l

# Check for broken wiki-links
# (Parse all [[link]] patterns and verify files exist)

# Generate wiki statistics
# (Count pages by type, list tags, etc.)
```

Run lint checks:
- After bulk ingestion of sources
- Before major wiki reorganization
- Weekly during active research periods
- When queries return unexpected gaps

---

## Section 9: Guardrails

This section defines operational constraints and safety guardrails for working with the wiki.

**ALWAYS use tex** for math formula: $a_0 = 100$.
**DO NOT use unicode math**

### File Integrity

- Skip corrupted files during processing
- Log corrupted files for manual review
- Never attempt to repair corrupted files automatically
- Maintain checksums for critical source files when possible

### Source Preservation

- **Never delete sources** once added to raw/
- Sources are immutable and serve as the ground truth
- If a source is found to be incorrect, mark it as archived in its metadata
- Add a note explaining why the source is archived

### Entity Name Verification

**DO NOT GUESS PERSON NAMES**. When creating entity pages for people:

- **Do NOT make up names** for initials, acronyms, or abbreviated author lists
- **Verify with user** when uncertain about name expansions or transliterations
- **Use web search tools** for names if neccessary

---

## Section 10: File Naming Conventions

### Wiki Pages

All wiki pages should use **kebab-case** (lowercase words separated by hyphens):
```
wiki/sources/2024-01-15-attention-is-all-you-need.md
wiki/entities/google-deepmind.md
wiki/concepts/transformer-architecture.md
wiki/synthesis/evolution-of-llm-architectures.md
```

### Source Files in Raw/

Source files in raw/ should preserve original names when possible, or use kebab-case with date prefix:
```
raw/papers/2024-01-15-paper-title.pdf
raw/articles/2024-02-01-article-slug.md
raw/assets/source-name/diagram-1.png
```

### Wiki Link Conventions

Use simple `[[page-name]]` format for all wiki links:
- The page name should match the filename (without .md extension)
- Use the pipe syntax `[[page-name|Display Name]]` for custom display text
- Examples:
  - `[[example-research-intro]]` - Simple link to a source
  - `[[sarah-chen|Dr. Sarah Chen]]` - Link with display name
  - `[[emergent-knowledge-structures]]` - Link to a concept
- **Do NOT** include directory prefixes in wiki-links (e.g., avoid `[[sources/page-name]]`)

### Index and Special Files

- `index.md` - Master index (lowercase, no date)
- `AGENTS.md` - Protocol document (uppercase for visibility)

### Forbidden Characters

Never use in filenames:
- Spaces (use hyphens)
- Special characters: `<>:"/\|?*`
- Leading or trailing periods
- Multiple consecutive hyphens

### Case Sensitivity

- All wiki filenames should be lowercase
- Exceptions: AGENTS.md (protocol document), README.md (if present)
- Cross-platform compatibility requires consistent casing
