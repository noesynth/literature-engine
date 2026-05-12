# literature-engine

**Claude Code skill for rigorous academic literature research — enforces full-text reading, prevents shallow abstract-only analysis.**

- 📚 **Full-text enforcement** — never settle for abstracts; every research task must read actual papers
- 🔍 **Dual-source architecture** — alphaxiv for full-text content, semantic-scholar for metadata and citation graphs
- 🔗 **Citation graph traversal** — trace references and citations to discover related work systematically
- 🎯 **Smart recommendations** — find similar papers using positive/negative seed papers
- 📄 **Structured workflows** — three research SOPs (metadata-first, direct full-text, citation tracing)

## What is this?

This is a [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code) repository that provides a structured Standard Operating Procedure (SOP) for academic literature research. It orchestrates two MCP servers — **alphaxiv** for reading full paper content and **semantic-scholar** for metadata/citation lookups — into a unified research workflow.

The core design principle: **semantic-scholar returns only metadata (title, abstract, authors, citations). Completing research with only abstracts is prohibited.** The skill enforces that every research task reads full paper text via alphaxiv before drawing conclusions.

This matters because AI assistants naturally take shortcuts — searching a few papers, reading abstracts, and declaring the research "done." literature-engine prevents this by embedding hard gates that block shallow research patterns.

## Quick Start

### 1. Configure MCP Servers

Add both MCP servers to your `.mcp.json` or `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "alphaxiv": {
      "type": "sse",
      "url": "https://mcp.alphaxiv.org"
    },
    "semantic-scholar": {
      "command": "npx",
      "args": ["@noesynth/semantic-scholar-mcp"],
      "env": {
        "SS_API_KEY": "optional-but-recommended"
      }
    }
  }
}
```

### 2. Get API Keys

- **alphaxiv**: No key required (public SSE endpoint)
- **semantic-scholar**: [Get free key here](https://www.semanticscholar.org/product/api) — increases rate limit from 1 req/s to 100 req/s

### 3. Use the Skill

Invoke in Claude Code:

```
/skill literature-search
```

Or reference in your project's `CLAUDE.md`:

```markdown
For literature research, use the literature-search skill from literature-engine.
```

## How It Works

### The Hard Gate

```
┌─────────────────────────────────────────────────────────────────┐
│  HARD GATE: semantic-scholar returns ONLY metadata.             │
│                                                                 │
│  If you find papers via relevanceSearch or paper:               │
│    1. Identify key papers from metadata                         │
│    2. Read full text via alphaxiv.get_paper_content             │
│    3. Base analysis on full-text content, NOT abstracts         │
│                                                                 │
│  Completing research with only abstracts is PROHIBITED.         │
└─────────────────────────────────────────────────────────────────┘
```

### Research Workflows

**Workflow 1: Metadata-First Search (Recommended)**

```
semantic-scholar.relevanceSearch(query)     →  Find papers by keyword
    ↓ Filter by citation count, year, relevance
alphaxiv.get_paper_content(arxiv_url)       →  Read full text
    ↓ Optionally expand
semantic-scholar.citations/references       →  Trace citation graph
    ↓ Repeat for promising papers
```

**Workflow 2: Direct Full-Text Search**

```
alphaxiv.discover_papers(keywords, question)  →  Semantic search on arXiv
    ↓
alphaxiv.get_paper_content(url)               →  Read selected papers
```

**Workflow 3: Citation Graph Exploration**

```
Start with known paper
    ↓
semantic-scholar.citations(paper_id)    →  Who cites this?
semantic-scholar.references(paper_id)   →  What does it cite?
    ↓ Filter and select
alphaxiv.get_paper_content              →  Read promising papers
    ↓ Iterate until sufficient coverage
```

## Available Tools

### alphaxiv (full-text, arXiv papers only)

| Tool | Purpose |
|------|---------|
| `discover_papers` | Semantic search with multi-round retrieval |
| `get_paper_content` | Full paper as markdown or AI-generated report |
| `answer_pdf_queries` | Ask specific questions about paper content |
| `read_files_from_github_repository` | Read code from paper's GitHub repo |

### semantic-scholar (metadata + citation graph)

| Tool | Purpose |
|------|---------|
| `paper` / `paperBatch` | Get paper metadata by ID (supports arXiv, DOI, S2 ID, PMID) |
| `relevanceSearch` | Keyword search with filters (year, field, citations, open access) |
| `citations` / `references` | Citation graph traversal (incoming/outgoing) |
| `recommendations` | Find similar papers based on seed papers |
| `author` / `authorPapers` | Author profiles and publication lists |

## Example Queries

Ask Claude things like:

- *"Survey recent papers on diffusion models for video generation, read the top 5 in full"*
- *"Find papers that cite Attention Is All You Need and focus on efficient attention — read the most influential ones"*
- *"What methods does the FlashAttention paper use? Read the full text and explain the IO-awareness approach"*
- *"Trace the citation graph from the LoRA paper — what adaptations have been proposed since?"*
- *"Find papers by Tri Dao, read his FlashAttention and Mamba papers, and compare the approaches"*

## Scope and Limitations

**Covered:**
- arXiv papers (computer science, mathematics, physics, statistics, quantitative biology/finance, electrical engineering)
- Any paper with metadata in Semantic Scholar (200M+ papers)

**Not covered:**
- Full-text reading of non-arXiv papers (PubMed, Nature, Cell) — metadata only via semantic-scholar
- Papers behind paywalls without open access versions

## Project Structure

```
literature-engine/
├── skills/
│   └── literature-search/
│       └── SKILL.md          # The SOP — full procedure with rules and examples
├── assets/
│   └── repo-info.txt         # Repository metadata
├── README.md                 # This file
├── LICENSE                   # Apache-2.0
└── .gitignore
```

## Links

- 📦 [semantic-scholar MCP](https://www.npmjs.com/package/@noesynth/semantic-scholar-mcp) — the metadata/citation MCP server
- 🔬 [alphaxiv MCP](https://mcp.alphaxiv.org) — full-text paper reading
- 📚 [Semantic Scholar API Docs](https://api.semanticscholar.org/api-docs/graph)
- 🔧 [Model Context Protocol](https://modelcontextprotocol.io)
- 🧠 [Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code)

## License

[Apache-2.0](LICENSE)
