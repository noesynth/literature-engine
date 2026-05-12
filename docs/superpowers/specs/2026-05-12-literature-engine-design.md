# literature-engine Design Spec

**Date:** 2026-05-12  
**Version:** v0.1.0  
**Status:** Draft

## Overview

literature-engine is a lightweight skill-only repository that provides guidance for using alphaxiv and semantic-scholar MCP servers in academic literature research. It enforces a critical rule: **never rely solely on abstracts from semantic-scholar for research tasks** — full-text reading via alphaxiv is mandatory.

## Problem Statement

When conducting literature research with MCP tools, Claude Code may take shortcuts by:
- Using only semantic-scholar's `relevanceSearch` to get paper abstracts
- Treating abstracts as sufficient for understanding papers
- Skipping full-text reading to save time/tokens

This produces shallow research that misses critical details in methodology, results, and analysis sections.

## Solution

A single SOP-layer skill (`literature-search`) that:
1. Lists available tools from both MCP servers
2. Clearly defines semantic-scholar as **metadata-only** (title, abstract, authors, citations)
3. Enforces a hard rule: abstracts are NOT sufficient — must use alphaxiv for full-text reading
4. Provides typical workflow guidance

## Architecture

### Project Structure

```
literature-engine/
├── skills/
│   └── literature-search/
│       └── SKILL.md          # Single SOP skill
├── docs/
│   └── superpowers/
│       └── specs/
│           └── 2026-05-12-literature-engine-design.md
├── README.md                  # Setup instructions, MCP config
└── .gitignore
```

### Design Decisions

**Why a single SOP instead of multiple?**
- Simpler for users — one entry point
- The two tools are complementary, not alternatives
- Decision logic (when to use which) fits naturally in one document

**Why no caching/storage?**
- alphaxiv and semantic-scholar handle their own caching
- No need for intermediate storage layer
- Keeps the repo lightweight (skill-only)

**Why SOP layer, not tactic?**
- This is a leaf-node skill that wraps MCP tools directly
- No orchestration of multiple SOPs needed
- Matches DARE's architecture: SOP = direct tool wrapper

## Components

### SKILL.md Structure

**Frontmatter:**
```yaml
---
name: Literature Search
description: Unified literature search SOP — alphaxiv for full-text, semantic-scholar for metadata
type: sop
layer: sop
agents: [alphaxiv, semantic-scholar]
tools: 
  alphaxiv: [discover_papers, get_paper_content, answer_pdf_queries, read_files_from_github_repository]
  semantic-scholar: [paper, paperBatch, references, citations, recommendations, author, authorPapers, relevanceSearch]
input: query (string), searchMode (metadata | fulltext | hybrid)
output: PaperResult[] with metadata and/or full content
---
```

**Content Sections:**
1. **Tool Overview** — brief list of what each MCP provides
2. **Usage Rules** — the critical `<HARD-GATE>` rule about abstracts
3. **Typical Workflow** — discovery → selection → deep reading → citation tracing

### README.md

Provides:
- Project purpose
- MCP configuration examples for alphaxiv and semantic-scholar
- Link to SKILL.md
- Version and compatibility info

## Usage Rules (Critical)

```markdown
<HARD-GATE>
semantic-scholar returns ONLY metadata (title, abstract, authors, year, citations).

NEVER treat semantic-scholar abstracts as sufficient for research tasks.

If you use relevanceSearch or paper to find papers, you MUST:
1. Identify key papers from the metadata
2. Use alphaxiv get_paper_content to read the full text
3. Base your analysis on full-text content, not abstracts

Completing a research task with only semantic-scholar abstracts is PROHIBITED.
</HARD-GATE>
```

## Typical Workflow

1. **Discovery**: Use `semantic-scholar.relevanceSearch` to find relevant papers by keyword
2. **Selection**: Filter results by citation count, year, relevance score
3. **Deep Reading**: Use `alphaxiv.get_paper_content` to read full text of top papers
4. **Citation Tracing** (optional): Use `semantic-scholar.citations/references` to expand the search

## Tool Reference

### alphaxiv (arXiv papers only)

| Tool | Purpose |
|------|---------|
| `discover_papers` | Semantic search with multi-round retrieval, returns ranked papers |
| `get_paper_content` | Get full paper as markdown or AI-generated report |
| `answer_pdf_queries` | Ask specific questions about a paper's content |
| `read_files_from_github_repository` | Read code from paper's GitHub repo |

### semantic-scholar (metadata only)

| Tool | Purpose |
|------|---------|
| `paper` | Get metadata for a single paper (S2 ID, ARXIV:xxx, DOI:xxx, etc.) |
| `paperBatch` | Batch fetch up to 500 papers |
| `relevanceSearch` | Keyword search with filters (year, field, citations, open access) |
| `citations` | Get papers that cite this paper (incoming) |
| `references` | Get papers this paper cites (outgoing) |
| `recommendations` | Find similar papers based on seed papers |
| `author` | Author profile (h-index, affiliations, paper/citation counts) |
| `authorPapers` | All papers by a specific author |

## Non-Goals (v0.1.0)

- **No caching layer** — tools handle their own caching
- **No local storage** — no paper database or markdown cache
- **No apify integration** — deferred to future versions
- **No tactic/strategy layers** — single SOP only

## Future Considerations (v0.2.0+)

- Add apify integration for non-arXiv papers (bioRxiv, PubMed, etc.)
- Consider adding a tactic layer if workflow orchestration becomes complex
- Evaluate whether to add local paper tracking (which papers have been read in this session)

## Testing Strategy

Since this is a skill-only repo with no code:
- Manual testing: use the skill in a Claude Code session with both MCP servers configured
- Verify the `<HARD-GATE>` rule prevents abstract-only research
- Test typical workflows (discovery → reading → citation tracing)

## Dependencies

**Required MCP servers:**
- `alphaxiv` — for arXiv paper full-text access
- `@noesynth/semantic-scholar-mcp` — for paper metadata and citation graphs

**Configuration:**
Users must add both servers to their `.mcp.json` or `claude_desktop_config.json`.

## Success Criteria

1. Claude Code consistently uses alphaxiv for full-text reading after semantic-scholar discovery
2. No research tasks completed with only semantic-scholar abstracts
3. Clear, concise skill that users can understand in under 2 minutes
4. Works seamlessly with DARE's existing skill hierarchy (can be called by DARE tactics)

## Open Questions

None — design is finalized and approved.
