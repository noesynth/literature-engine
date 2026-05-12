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

# Literature Search SOP

## Layer Rules
- **Layer**: sop — wraps MCP tools directly
- **Called by**: Any tactic or strategy requiring literature search
- **Calls**: alphaxiv MCP tools, semantic-scholar MCP tools (never calls other SOPs)

## Tool Overview

### alphaxiv (arXiv papers only)

| Tool | Purpose |
|------|---------|
| `discover_papers` | Semantic search with multi-round retrieval, returns ranked papers |
| `get_paper_content` | Get full paper as markdown or AI-generated report |
| `answer_pdf_queries` | Ask specific questions about a paper's content |
| `read_files_from_github_repository` | Read code from paper's GitHub repo |

**Scope:** arXiv papers only (computer science, mathematics, physics, statistics, quantitative biology/finance, electrical engineering)

**NOT covered:** biomedical, clinical, or life science papers from PubMed, Cell, Nature

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

**Returns:** Title, abstract, authors, year, citation count, paper IDs (S2, arXiv, DOI)

**Does NOT return:** Full paper content, methodology details, results, analysis

## Usage Rules

<HARD-GATE>
**semantic-scholar returns ONLY metadata (title, abstract, authors, year, citations).**

**NEVER treat semantic-scholar abstracts as sufficient for research tasks.**

If you use `relevanceSearch` or `paper` to find papers, you MUST:
1. Identify key papers from the metadata
2. Use `alphaxiv.get_paper_content` to read the full text
3. Base your analysis on full-text content, not abstracts

**Completing a research task with only semantic-scholar abstracts is PROHIBITED.**
</HARD-GATE>

## Procedure

### Workflow 1: Metadata-First Search (Recommended)

1. **Discovery**: Use `semantic-scholar.relevanceSearch(query, limit, year, min_citation_count)`
   - Returns: paper metadata with abstracts
   - Filter by: citation count, year, relevance score

2. **Selection**: Identify top N papers (typically 5-10) based on:
   - Relevance to research question
   - Citation count (indicator of impact)
   - Recency (if needed)

3. **Deep Reading**: For each selected paper:
   - Use `alphaxiv.get_paper_content(url: arxiv_url)` to get full text
   - Or use `alphaxiv.answer_pdf_queries(url, queries)` for targeted questions

4. **Citation Tracing** (optional):
   - Use `semantic-scholar.citations(paper_id)` to find papers that cite it
   - Use `semantic-scholar.references(paper_id)` to find papers it cites
   - Repeat steps 2-3 for promising papers from the graph

### Workflow 2: Direct Full-Text Search

1. **Search**: Use `alphaxiv.discover_papers(keywords, question, difficulty)`
   - `keywords`: 3-4 concise terms (method names, acronyms, authors)
   - `question`: Detailed semantic description of desired papers
   - `difficulty`: 1-10 (higher = more retrieval effort, slower)

2. **Read**: Use `alphaxiv.get_paper_content(url)` for selected papers
   - `fullText: false` (default) — AI-generated report (faster, optimized for LLM)
   - `fullText: true` — raw extracted text (slower, complete content)

### Workflow 3: Citation Graph Exploration

1. **Start**: Begin with a known paper (paperId from semantic-scholar or arXiv URL)

2. **Expand**:
   - Use `semantic-scholar.citations(paper_id, limit, offset)` for incoming citations
   - Use `semantic-scholar.references(paper_id, limit, offset)` for outgoing references

3. **Read**: Use `alphaxiv.get_paper_content` for promising papers from the graph

4. **Iterate**: Repeat expansion and reading until sufficient coverage

## Tool-Specific Notes

### alphaxiv

**ID formats accepted:**
- arXiv URL: `https://arxiv.org/abs/2307.12307`
- arXiv PDF: `https://arxiv.org/pdf/2401.12345`
- alphaXiv URL: `https://alphaxiv.org/overview/2307.12307`

**get_paper_content options:**
- `fullText: false` — Returns AI-generated intermediate report (default, faster)
- `fullText: true` — Returns raw extracted text (slower, complete)

**answer_pdf_queries:**
- Accepts any PDF URL (not just arXiv)
- Returns filtered page content as XML
- Multiple queries on same paper are nearly free (cached)

### semantic-scholar

**ID formats accepted:**
- S2 ID: `649def34f8be52c8b66281af98ae884c09aef38b`
- arXiv: `ARXIV:1706.03762` or bare `1706.03762` (auto-prefixed)
- DOI: `DOI:10.1038/s41586-021-03819-2` or bare `10.xxx/...` (auto-prefixed)
- PMID: `PMID:12345678`
- URL: `URL:https://arxiv.org/abs/2307.12307`

**Pagination:**
- `citations` and `references`: max 1000 results, use `offset` and `limit` for pagination
- `paperBatch`: max 500 papers per call

**Filters (relevanceSearch):**
- `year`: "2023-2024", "2020-", "-2023"
- `fields_of_study`: "Computer Science", "Mathematics", etc.
- `min_citation_count`: integer threshold
- `open_access_only`: boolean

## Output Format

Return a structured list of papers with:
- **Metadata** (from semantic-scholar): title, authors, year, citations, paper IDs
- **Full Content** (from alphaxiv): markdown text or AI report
- **Source**: which tool provided which data

Example:
```json
{
  "papers": [
    {
      "title": "Attention Is All You Need",
      "authors": ["Vaswani et al."],
      "year": 2017,
      "citationCount": 50000,
      "paperId": "ARXIV:1706.03762",
      "abstract": "...",
      "fullText": "# Attention Is All You Need\n\n## Abstract\n...",
      "source": {
        "metadata": "semantic-scholar",
        "fullText": "alphaxiv"
      }
    }
  ]
}
```

## Error Handling

- **Paper not found**: semantic-scholar returns `{ error: "not_found" }`, alphaxiv returns error message
- **Rate limiting**: Both tools handle retries automatically (429 responses)
- **Non-arXiv papers**: alphaxiv will fail, use semantic-scholar metadata only (but flag as incomplete)

## Examples

### Example 1: Find and read papers on transformers

```
1. semantic-scholar.relevanceSearch("transformer attention mechanism", limit=10, year="2023-2024")
2. Filter top 5 by citation count
3. For each: alphaxiv.get_paper_content(arxiv_url)
4. Analyze full-text content
```

### Example 2: Deep dive on a specific paper

```
1. semantic-scholar.paper("ARXIV:1706.03762")  # Get metadata
2. alphaxiv.get_paper_content("https://arxiv.org/abs/1706.03762")  # Get full text
3. alphaxiv.answer_pdf_queries(url, ["What is the time complexity?", "How is positional encoding implemented?"])
```

### Example 3: Citation tracing

```
1. Start with paper: "ARXIV:1706.03762"
2. semantic-scholar.citations(paper_id, limit=50)  # Who cites this?
3. Filter by year >= 2023, citationCount >= 100
4. For top 10: alphaxiv.get_paper_content(arxiv_url)
5. Analyze how the original work has been extended
```
