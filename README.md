# literature-engine

Lightweight skill repository for academic literature research using alphaxiv and semantic-scholar MCP servers.

## Purpose

Provides guidance for Claude Code to:
- Use **alphaxiv** for full-text paper reading (arXiv only)
- Use **semantic-scholar** for paper metadata and citation graphs
- **Enforce** full-text reading — never rely solely on abstracts

## Installation

This is a skill-only repository. No npm installation required.

## Usage

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
        "SS_API_KEY": "<your-key-here>"
      }
    }
  }
}
```

**Get API keys:**
- alphaxiv: No key required (public SSE endpoint)
- semantic-scholar: [Get key here](https://www.semanticscholar.org/product/api) (optional, increases rate limit from 1 req/s to 100 req/s)

### 2. Use the Skill

In Claude Code, invoke the skill:

```
/skill literature-search
```

Or reference it in your project's `CLAUDE.md`:

```markdown
For literature research, use the literature-search skill from literature-engine.
```

## Skill Overview

**Single SOP:** `skills/literature-search/SKILL.md`

**Key Rule:**
> semantic-scholar returns ONLY metadata (title, abstract, authors, citations).  
> You MUST use alphaxiv to read full-text content.  
> Completing research with only abstracts is PROHIBITED.

**Typical Workflow:**
1. Discovery: `semantic-scholar.relevanceSearch` to find papers
2. Selection: Filter by citation count, year, relevance
3. Deep Reading: `alphaxiv.get_paper_content` for full text
4. Citation Tracing: `semantic-scholar.citations/references` to expand

## Tools Reference

### alphaxiv (arXiv papers only)
- `discover_papers` — semantic search with multi-round retrieval
- `get_paper_content` — full paper as markdown or AI report
- `answer_pdf_queries` — ask questions about paper content
- `read_files_from_github_repository` — read code from paper's repo

### semantic-scholar (metadata only)
- `paper` / `paperBatch` — get paper metadata by ID
- `relevanceSearch` — keyword search with filters
- `citations` / `references` — citation graph traversal
- `recommendations` — find similar papers
- `author` / `authorPapers` — author info and publications

## Version

**v0.1.0** — Initial release with alphaxiv + semantic-scholar support

## License

[Apache-2.0](LICENSE)
