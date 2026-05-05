---
name: research-survey
description: Trigger when user wants to conduct a literature survey on a research topic. Use for finding and summarizing relevant papers.
---

# Research Survey

## Overview

Conduct a structured literature survey on a given research topic. The goal is to
map the landscape, identify key works, and synthesize trends — not to read every
paper in full detail.

## Prerequisites

- User provides a research topic or question.

## Process

### Step 1: Clarify the Topic

Ask the user to specify:
- The research topic or question.
- Any constraints on venue, year range, or domain.
- Whether they have seed papers to start from.

If the topic is vague, help narrow it by proposing 3-5 sub-topics and asking
the user to pick or refine.

### Step 2: Search for Papers

Use web search and academic search tools to find relevant papers.

**Venue priority** (highest to lowest):
1. CVPR, ICCV, ECCV (Vision)
2. NeurIPS, ICML, ICLR (ML)
3. ACL, EMNLP (NLP)
4. MLSys, OSDI, SOSP (Systems)

**Filters:**
- Prefer papers with open-source code (GitHub links).
- Prefer recent papers (last 3 years) unless the user specifies otherwise.
- Prefer LaTeX/HTML source over PDF when available.

**Search strategy:**
- Search by keyword combinations.
- Follow citation trails from seed papers.
- Check related work sections of the most relevant papers.
- Search for survey papers on the topic first — they provide a roadmap.

### Step 3: Screen Papers

At this stage, **only read the abstract and introduction** of each paper.
Do not deep-read the full paper yet.

For each paper, extract:
- Title
- Authors (first + last + "et al." if many)
- Venue and year
- Code link (if available)
- One-paragraph summary (from abstract + intro)
- Relevance to the user's topic (HIGH / MEDIUM / LOW)
- Key insight (one sentence)

### Step 4: Output Structured Survey

Present results in a structured format:

```
## Survey: [Topic]

### Sub-topic: [Name]

| # | Title | Venue | Year | Code | Relevance | Key Insight |
|---|-------|-------|------|------|-----------|-------------|
| 1 | ...   | ...   | ...  | ...  | HIGH      | ...         |

#### Paper Summaries

**[Author et al., Venue Year] Title**
- Summary: ...
- Relevance: HIGH — [why]
- Key insight: ...
- Code: [link or "none"]
```

Group papers by sub-topic. Within each sub-topic, sort by relevance (HIGH first).

### Step 5: Synthesis

End the survey with a synthesis section:

- **Trends:** What directions are gaining traction?
- **Gaps:** What problems are under-explored?
- **Opportunities:** Where might novel contributions be possible?
- **Recommended reading:** Top 3-5 papers the user should read in full.

## Rules

- Do NOT fabricate citations. Only include papers you have actually found.
- If a paper's relevance is unclear, mark it MEDIUM and note why.
- Do NOT read full papers at this stage — abstract + intro only.
- If fewer than 5 relevant papers are found, expand the search or report honestly.
- Prefer breadth (covering the landscape) over depth (reading every paper fully).
