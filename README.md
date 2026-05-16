# literature-engine

Claude Code skills for rigorous academic literature research — enforces full-text reading, prevents shallow abstract-only analysis.

## Skill Tiers

| Skill | Depth | When to Use |
|-------|-------|-------------|
| `literature-overview` | Shallow | Quick landscape scan — what papers exist on a topic |
| `literature-search` | Medium | Literature survey — read AI summaries, understand methods and contributions |
| `literature-research` | Deep | Rigorous analysis — read raw full text, extract specific details |

## How It Works

Each skill enforces a HARD-GATE appropriate to its depth:

| Tier | HARD-GATE Rule |
|------|---------------|
| Overview | Do NOT draw conclusions from abstracts — orientation only |
| Search | MUST read AI summary report for every paper analyzed |
| Research | MUST read raw full text — AI summaries not acceptable |

This prevents the common failure mode where an AI assistant treats abstracts as sufficient evidence for research conclusions.

## Tool Responsibility Matrix

| Tool | Role |
|------|------|
| `alphaxiv.discover_papers` | Primary search — arXiv semantic search |
| `ss.relevanceSearch` | Supplementary search — non-arXiv papers |
| `ss.paper / ss.paperBatch` | Metadata enrichment — citation count, DOI, S2 ID |
| `ss.citations / ss.references` | Citation graph traversal |
| `alphaxiv.get_paper_content` (fullText: false) | AI-generated summary report |
| `alphaxiv.get_paper_content` (fullText: true) | Raw full text |
| `alphaxiv.answer_pdf_queries` | Targeted questions against PDF content |

## MCP Server Setup

### alphaxiv

```json
{
  "mcpServers": {
    "alphaxiv": {
      "type": "sse",
      "url": "https://mcp.alphaxiv.org"
    }
  }
}
```

No API key required (public SSE endpoint).

**Scope:** arXiv papers only (computer science, mathematics, physics, statistics, quantitative biology/finance, electrical engineering). Does NOT cover biomedical, clinical, or life science papers.

### semantic-scholar

```json
{
  "mcpServers": {
    "semantic-scholar": {
      "command": "npx",
      "args": ["@yogsoth-ai/semantic-scholar-mcp"],
      "env": {
        "SS_API_KEY": "<your-key-here>"
      }
    }
  }
}
```

Get an API key at: https://www.semanticscholar.org/product/api (optional — increases rate limit from 1 req/s to 100 req/s)

## Usage

### literature-overview (quick scan)

Use when you need to:
- Quickly assess how much literature exists on a topic
- Identify key papers and authors
- Get citation counts to gauge impact
- Decide which papers deserve deeper reading

### literature-search (survey)

Use when you need to:
- Conduct a literature survey
- Understand what methods exist and how they compare
- Identify gaps in current research
- Build background knowledge

### literature-research (deep analysis)

Use when you need to:
- Understand exact methodology details (equations, algorithms)
- Extract specific experimental setup (hyperparameters, datasets)
- Design experiments based on prior work
- Write a paper that cites specific claims with precision

## Project Structure

```
literature-engine/
├── skills/
│   ├── literature-overview/
│   │   └── SKILL.md          # Shallow — landscape scan
│   ├── literature-search/
│   │   └── SKILL.md          # Medium — AI summary reading
│   └── literature-research/
│       └── SKILL.md          # Deep — raw full text reading
├── tests/
│   └── integration-prompt.md # Live integration test prompt
├── assets/
│   └── repo-info.txt
├── README.md
├── .gitignore
└── LICENSE
```

## Version

**v1.1.0** — Three depth-tiered skills (overview, search, research)

## 📄 License

[Apache-2.0](LICENSE)
