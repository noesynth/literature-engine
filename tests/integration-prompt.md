# Integration Test Prompt

Use this prompt in a Claude Code session with literature-engine skills to test all three depth tiers against live MCP servers.

## Prerequisites

- alphaxiv MCP configured (SSE: https://mcp.alphaxiv.org)
- semantic-scholar MCP configured (@yogsoth-ai/semantic-scholar-mcp)
- SS_API_KEY recommended for higher rate limits (100 req/s vs 1 req/s)

## Test Topic

**"graph neural networks for molecular property prediction"**

This topic is ideal because:
- Well-covered on arXiv (CS + computational biology intersection)
- Has high-citation foundational papers
- Active research area with recent publications
- Tests both alphaxiv (arXiv papers) and ss (broader venue coverage)

## Test Papers

| # | Paper | Expected Source | Why |
|---|-------|----------------|-----|
| 1 | Neural Message Passing for Quantum Chemistry (MPNN) | arXiv + SS | Foundational GNN for molecules |
| 2 | SchNet: A continuous-filter convolutional neural network | arXiv + SS | Key architecture |
| 3 | Directional Message Passing for Molecular Graphs (DimeNet) | arXiv + SS | Geometric GNN |
| 4 | Equivariant Message Passing (PaiNN) | arXiv + SS | Equivariant approach |
| 5 | GemNet: Universal Directional Graph Neural Networks | arXiv + SS | State-of-art geometric |
| 6 | Uni-Mol: A Universal 3D Molecular Pretraining Framework | arXiv + SS | Pretraining approach |
| 7 | MoleculeNet: A Benchmark for Molecular Machine Learning | SS only | Benchmark paper (may not be on arXiv) |

## Test Plan

### 1. literature-overview

**Invoke:** Use the literature-overview skill

**Prompt:** "Give me a quick overview of the landscape of graph neural networks for molecular property prediction. What papers exist? Who are the key authors?"

**Expected behavior:**
- Calls `alphaxiv.discover_papers` with relevant keywords (GNN, molecular, property prediction)
- Calls `ss.relevanceSearch` to supplement with non-arXiv papers
- Returns a list of papers with titles, authors, years, citation counts
- Does NOT call `alphaxiv.get_paper_content`
- Does NOT draw conclusions about methodology or results
- Clearly presents results as a landscape overview

**Failure conditions:**
- Calls get_paper_content (wrong depth tier)
- Draws conclusions about specific methods from abstracts
- Claims to understand how a method works based on abstract alone
- Presents abstract content as authoritative analysis

---

### 2. literature-search

**Invoke:** Use the literature-search skill

**Prompt:** "Conduct a literature survey on graph neural networks for molecular property prediction. I need to understand the main approaches, their contributions, and how the field has evolved."

**Expected behavior:**
- Calls `alphaxiv.discover_papers` for primary search
- Calls `ss.relevanceSearch` for supplementary coverage
- Calls `ss.paperBatch` to enrich metadata (citation counts)
- Selects 5-15 papers for reading
- Calls `alphaxiv.get_paper_content(fullText: false)` for EACH selected paper
- Bases analysis on AI summary report content (not just abstracts)
- Provides structured survey with methods, contributions, evolution

**Failure conditions:**
- Completes survey using only abstracts from discover_papers or relevanceSearch
- Reads fewer than 5 papers via get_paper_content
- Uses get_paper_content with fullText: true (wrong depth — that's literature-research)
- Draws methodology conclusions without reading the AI summary

---

### 3. literature-research

**Invoke:** Use the literature-research skill

**Prompt:** "I'm designing an experiment to compare geometric GNN architectures for molecular property prediction. I need to understand the exact architectures, training procedures, and evaluation protocols of DimeNet, PaiNN, and GemNet. Extract specific hyperparameters and dataset details."

**Expected behavior:**
- Calls `alphaxiv.discover_papers` to find the specific papers
- Calls `alphaxiv.get_paper_content(fullText: true)` for DimeNet, PaiNN, GemNet (minimum 3)
- Calls `alphaxiv.answer_pdf_queries` for specific details:
  - Exact architecture details (layers, dimensions, activation functions)
  - Training hyperparameters (learning rate, batch size, optimizer)
  - Evaluation datasets and metrics
  - Ablation study results
- Bases analysis on raw full text content
- Provides specific numbers, equations, and technical details

**Failure conditions:**
- Uses get_paper_content with fullText: false (AI summaries not acceptable at this depth)
- Reads fewer than 3 papers in full text
- Claims specific numbers without reading the results section
- Does not use answer_pdf_queries for targeted extraction
- Provides vague descriptions instead of specific technical details

---

## HARD-GATE Enforcement Verification

| Check | Overview | Search | Research |
|-------|----------|--------|----------|
| get_paper_content called? | NO | YES (fullText: false) | YES (fullText: true) |
| answer_pdf_queries called? | NO | NO | YES |
| Conclusions from abstracts? | NO (orientation only) | NO (from AI reports) | NO (from full text) |
| Minimum papers read | 0 | 5 | 3 |
| Depth of analysis | Landscape only | Methods + contributions | Exact details + numbers |

## Edge Cases

- **Non-arXiv paper (MoleculeNet):** Should appear in ss.relevanceSearch results. Cannot be read via alphaxiv. In literature-search/research, note it as "metadata only — full text unavailable via alphaxiv."
- **Paper not found:** If discover_papers returns no results for a specific query, fall back to ss.relevanceSearch with broader terms.
- **Rate limiting:** If ss returns 429, wait and retry. alphaxiv handles retries internally.
- **Large result sets:** discover_papers with high difficulty may return many papers — selection step should filter effectively.

## Success Criteria

1. Each skill tier uses ONLY its designated tools (no tool leakage between tiers)
2. HARD-GATEs are respected — no depth-tier violations
3. literature-overview never reads paper content
4. literature-search reads AI summaries for every analyzed paper (minimum 5)
5. literature-research reads raw full text (minimum 3) and uses answer_pdf_queries
6. All three skills produce useful, structured output appropriate to their depth
7. Non-arXiv papers are handled gracefully (metadata only, flagged as incomplete)
