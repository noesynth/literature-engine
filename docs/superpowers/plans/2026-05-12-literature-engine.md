# literature-engine Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a lightweight skill-only repository that enforces full-text reading via alphaxiv and prevents reliance on semantic-scholar abstracts alone.

**Architecture:** Single SOP-layer skill (`literature-search/SKILL.md`) that wraps alphaxiv and semantic-scholar MCP tools with clear usage rules and a mandatory HARD-GATE preventing abstract-only research.

**Tech Stack:** Markdown skills (no code), alphaxiv MCP, semantic-scholar MCP

---

## File Structure

```
literature-engine/
├── skills/
│   └── literature-search/
│       └── SKILL.md                    # Core SOP skill with tool guidance
├── docs/
│   └── superpowers/
│       └── specs/
│           └── 2026-05-12-literature-engine-design.md  # Already exists
├── README.md                            # Project overview and MCP setup
├── .gitignore                           # Standard Node.js template
└── LICENSE                              # Apache-2.0
```

---

### Task 1: Initialize Git Repository

**Files:**
- Create: `.gitignore`
- Create: `LICENSE`

- [ ] **Step 1: Initialize git repository**

```bash
cd G:\NOESYNTH\literature-engine
git init
git branch -m main
```

Expected: Initialized empty Git repository

- [ ] **Step 2: Create .gitignore**

Create `.gitignore` with Node.js template content:

```gitignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*

# Diagnostic reports
report.[0-9]*.[0-9]*.[0-9]*.[0-9]*.json

# Runtime data
pids
*.pid
*.seed
*.pid.lock

# Coverage directory
coverage
*.lcov

# nyc test coverage
.nyc_output

# Dependency directories
node_modules/
jspm_packages/

# TypeScript cache
*.tsbuildinfo

# Optional npm cache directory
.npm

# Optional eslint cache
.eslintcache

# dotenv environment variable files
.env
.env.*
!.env.example

# parcel-bundler cache
.cache
.parcel-cache

# Next.js build output
.next
out

# Nuxt.js build / generate output
.nuxt
dist
.output

# vuepress build output
.vuepress/dist

# Docusaurus cache
.docusaurus

# Serverless directories
.serverless/

# FuseBox cache
.fusebox/

# DynamoDB Local files
.dynamodb/

# TernJS port file
.tern-port

# Stores VSCode versions
.vscode-test

# yarn v3
.yarn/*
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions

# OS files
.DS_Store
Thumbs.db
```

- [ ] **Step 3: Create LICENSE**

Create `LICENSE` with Apache-2.0 license:

```
                                 Apache License
                           Version 2.0, January 2004
                        http://www.apache.org/licenses/

   TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION

   1. Definitions.

      "License" shall mean the terms and conditions for use, reproduction,
      and distribution as defined by Sections 1 through 9 of this document.

      "Licensor" shall mean the copyright owner or entity authorized by
      the copyright owner that is granting the License.

      "Legal Entity" shall mean the union of the acting entity and all
      other entities that control, are controlled by, or are under common
      control with that entity. For the purposes of this definition,
      "control" means (i) the power, direct or indirect, to cause the
      direction or management of such entity, whether by contract or
      otherwise, or (ii) ownership of fifty percent (50%) or more of the
      outstanding shares, or (iii) beneficial ownership of such entity.

      "You" (or "Your") shall mean an individual or Legal Entity
      exercising permissions granted by this License.

      "Source" form shall mean the preferred form for making modifications,
      including but not limited to software source code, documentation
      source, and configuration files.

      "Object" form shall mean any form resulting from mechanical
      transformation or translation of a Source form, including but
      not limited to compiled object code, generated documentation,
      and conversions to other media types.

      "Work" shall mean the work of authorship, whether in Source or
      Object form, made available under the License, as indicated by a
      copyright notice that is included in or attached to the work
      (an example is provided in the Appendix below).

      "Derivative Works" shall mean any work, whether in Source or Object
      form, that is based on (or derived from) the Work and for which the
      editorial revisions, annotations, elaborations, or other modifications
      represent, as a whole, an original work of authorship. For the purposes
      of this License, Derivative Works shall not include works that remain
      separable from, or merely link (or bind by name) to the interfaces of,
      the Work and Derivative Works thereof.

      "Contribution" shall mean any work of authorship, including
      the original version of the Work and any modifications or additions
      to that Work or Derivative Works thereof, that is intentionally
      submitted to Licensor for inclusion in the Work by the copyright owner
      or by an individual or Legal Entity authorized to submit on behalf of
      the copyright owner. For the purposes of this definition, "submitted"
      means any form of electronic, verbal, or written communication sent
      to the Licensor or its representatives, including but not limited to
      communication on electronic mailing lists, source code control systems,
      and issue tracking systems that are managed by, or on behalf of, the
      Licensor for the purpose of discussing and improving the Work, but
      excluding communication that is conspicuously marked or otherwise
      designated in writing by the copyright owner as "Not a Contribution."

      "Contributor" shall mean Licensor and any individual or Legal Entity
      on behalf of whom a Contribution has been received by Licensor and
      subsequently incorporated within the Work.

   2. Grant of Copyright License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      copyright license to reproduce, prepare Derivative Works of,
      publicly display, publicly perform, sublicense, and distribute the
      Work and such Derivative Works in Source or Object form.

   3. Grant of Patent License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      (except as stated in this section) patent license to make, have made,
      use, offer to sell, sell, import, and otherwise transfer the Work,
      where such license applies only to those patent claims licensable
      by such Contributor that are necessarily infringed by their
      Contribution(s) alone or by combination of their Contribution(s)
      with the Work to which such Contribution(s) was submitted. If You
      institute patent litigation against any entity (including a
      cross-claim or counterclaim in a lawsuit) alleging that the Work
      or a Contribution incorporated within the Work constitutes direct
      or contributory patent infringement, then any patent licenses
      granted to You under this License for that Work shall terminate
      as of the date such litigation is filed.

   4. Redistribution. You may reproduce and distribute copies of the
      Work or Derivative Works thereof in any medium, with or without
      modifications, and in Source or Object form, provided that You
      meet the following conditions:

      (a) You must give any other recipients of the Work or
          Derivative Works a copy of this License; and

      (b) You must cause any modified files to carry prominent notices
          stating that You changed the files; and

      (c) You must retain, in the Source form of any Derivative Works
          that You distribute, all copyright, patent, trademark, and
          attribution notices from the Source form of the Work,
          excluding those notices that do not pertain to any part of
          the Derivative Works; and

      (d) If the Work includes a "NOTICE" text file as part of its
          distribution, then any Derivative Works that You distribute must
          include a readable copy of the attribution notices contained
          within such NOTICE file, excluding those notices that do not
          pertain to any part of the Derivative Works, in at least one
          of the following places: within a NOTICE text file distributed
          as part of the Derivative Works; within the Source form or
          documentation, if provided along with the Derivative Works; or,
          within a display generated by the Derivative Works, if and
          wherever such third-party notices normally appear. The contents
          of the NOTICE file are for informational purposes only and
          do not modify the License. You may add Your own attribution
          notices within Derivative Works that You distribute, alongside
          or as an addendum to the NOTICE text from the Work, provided
          that such additional attribution notices cannot be construed
          as modifying the License.

      You may add Your own copyright statement to Your modifications and
      may provide additional or different license terms and conditions
      for use, reproduction, or distribution of Your modifications, or
      for any such Derivative Works as a whole, provided Your use,
      reproduction, and distribution of the Work otherwise complies with
      the conditions stated in this License.

   5. Submission of Contributions. Unless You explicitly state otherwise,
      any Contribution intentionally submitted for inclusion in the Work
      by You to the Licensor shall be under the terms and conditions of
      this License, without any additional terms or conditions.
      Notwithstanding the above, nothing herein shall supersede or modify
      the terms of any separate license agreement you may have executed
      with Licensor regarding such Contributions.

   6. Trademarks. This License does not grant permission to use the trade
      names, trademarks, service marks, or product names of the Licensor,
      except as required for reasonable and customary use in describing the
      origin of the Work and reproducing the content of the NOTICE file.

   7. Disclaimer of Warranty. Unless required by applicable law or
      agreed to in writing, Licensor provides the Work (and each
      Contributor provides its Contributions) on an "AS IS" BASIS,
      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
      implied, including, without limitation, any warranties or conditions
      of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A
      PARTICULAR PURPOSE. You are solely responsible for determining the
      appropriateness of using or redistributing the Work and assume any
      risks associated with Your exercise of permissions under this License.

   8. Limitation of Liability. In no event and under no legal theory,
      whether in tort (including negligence), contract, or otherwise,
      unless required by applicable law (such as deliberate and grossly
      negligent acts) or agreed to in writing, shall any Contributor be
      liable to You for damages, including any direct, indirect, special,
      incidental, or consequential damages of any character arising as a
      result of this License or out of the use or inability to use the
      Work (including but not limited to damages for loss of goodwill,
      work stoppage, computer failure or malfunction, or any and all
      other commercial damages or losses), even if such Contributor
      has been advised of the possibility of such damages.

   9. Accepting Warranty or Additional Liability. While redistributing
      the Work or Derivative Works thereof, You may choose to offer,
      and charge a fee for, acceptance of support, warranty, indemnity,
      or other liability obligations and/or rights consistent with this
      License. However, in accepting such obligations, You may act only
      on Your own behalf and on Your sole responsibility, not on behalf
      of any other Contributor, and only if You agree to indemnify,
      defend, and hold each Contributor harmless for any liability
      incurred by, or claims asserted against, such Contributor by reason
      of your accepting any such warranty or additional liability.

   END OF TERMS AND CONDITIONS

   Copyright 2026 NOESYNTH

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
```

- [ ] **Step 4: Commit**

```bash
git add .gitignore LICENSE
git commit -m "chore: initialize repository with .gitignore and LICENSE

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

---

### Task 2: Create README

**Files:**
- Create: `README.md`

- [ ] **Step 1: Write README.md**

Create `README.md`:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with setup instructions and tool overview

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

---

### Task 3: Create SKILL.md

**Files:**
- Create: `skills/literature-search/SKILL.md`

- [ ] **Step 1: Write SKILL.md frontmatter and overview**

Create `skills/literature-search/SKILL.md`:

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add skills/literature-search/SKILL.md
git commit -m "feat: add literature-search SOP skill

- Wraps alphaxiv and semantic-scholar MCP tools
- Enforces full-text reading via HARD-GATE rule
- Provides 3 workflows: metadata-first, direct search, citation tracing
- Includes tool reference and usage examples

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

---

### Task 4: Final Verification

**Files:**
- Verify: All files created and committed

- [ ] **Step 1: Verify file structure**

Run:
```bash
cd G:\NOESYNTH\literature-engine
ls -R
```

Expected output:
```
.:
.gitignore  LICENSE  README.md  docs/  skills/

./docs:
superpowers/

./docs/superpowers:
plans/  specs/

./docs/superpowers/specs:
2026-05-12-literature-engine-design.md

./skills:
literature-search/

./skills/literature-search:
SKILL.md
```

- [ ] **Step 2: Verify git status**

Run:
```bash
git status
```

Expected: `nothing to commit, working tree clean`

- [ ] **Step 3: Verify commit history**

Run:
```bash
git log --oneline
```

Expected: 3 commits (initialize, README, SKILL.md)

- [ ] **Step 4: Read SKILL.md to verify HARD-GATE rule**

Run:
```bash
grep -A 10 "HARD-GATE" skills/literature-search/SKILL.md
```

Expected: HARD-GATE block with prohibition on abstract-only research

---

## Self-Review

**Spec coverage check:**
- ✓ Single SOP skill created (`literature-search/SKILL.md`)
- ✓ Frontmatter with tools list
- ✓ HARD-GATE rule preventing abstract-only research
- ✓ Tool overview for alphaxiv and semantic-scholar
- ✓ Three workflows (metadata-first, direct, citation tracing)
- ✓ README with MCP setup instructions
- ✓ .gitignore and LICENSE
- ✓ No caching/storage (as specified in non-goals)

**Placeholder scan:**
- ✓ No TBD, TODO, or "implement later"
- ✓ All code blocks complete
- ✓ All file paths exact
- ✓ All commands with expected output

**Type consistency:**
- ✓ Tool names consistent across README and SKILL.md
- ✓ MCP server names match (`alphaxiv`, `semantic-scholar`)
- ✓ Frontmatter fields match DARE conventions

---

## Execution Handoff

Plan complete and saved to `docs/superpowers/plans/2026-05-12-literature-engine.md`. Two execution options:

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks in this session using executing-plans, batch execution with checkpoints

Which approach?
