---
name: research-survey
description: Conduct a comprehensive literature survey on a research topic. Searches for top-venue papers with code, summarizes findings, and identifies trends and opportunities.
---

# Research Survey

## Trigger

Use this skill when the user wants to begin researching a new topic, understand the landscape of a field, or prepare for a research project by surveying existing work.

## Prerequisites

- None. This is the entry point of the research workflow.

## Instructions

### 1. Clarify the Topic

Ask the user for their research topic or area of interest. If the topic is broad, help narrow it to a specific research question or direction. Request any constraints on venue, year range, or domain, and whether they have seed papers to start from.

If the topic is vague, propose 3-5 sub-topics and ask the user to pick or refine.

### 2. Search Strategy

Search for relevant papers with the following priorities:

**Venue priority** (top venues only):
- Computer Vision: CVPR, ICCV, ECCV
- Machine Learning: NeurIPS, ICML, ICLR
- NLP: ACL, EMNLP
- Equivalent top-tier venues in other domains

**Paper priority** (within venue):
1. Papers with publicly available code (GitHub or similar)
2. Papers from top-tier venues in the last 3 years
3. Highly influential older papers (foundational work)

**Format priority**:
- Prefer LaTeX or Markdown source over PDF
- Use arXiv source files when available
- Only fall back to PDF when no other format exists

**Search approach**:
- Search by keyword combinations
- Follow citation trails from seed papers
- Check related work sections of the most relevant papers
- Search for survey papers on the topic first as a roadmap

### 3. Reading Strategy

At the survey stage, only read the **abstract and introduction** of each paper. Do not deep-dive into methods, experiments, or appendices at this point. The goal is breadth, not depth.

### 4. Output Format

Produce a structured survey with the following entry for each paper:

```
### [Paper Title]
- **Venue**: [Conference/Journal, Year]
- **Code**: [Link or "None"]
- **Summary**: [2-3 sentences capturing the core contribution]
- **Relevance**: [High / Medium / Low]
- **Key Insight**: [One sentence: what makes this paper interesting or useful]
```

### 5. Organization

Group papers by sub-topic or thematic cluster. Each cluster should have a brief paragraph explaining the theme.

### 6. Synthesis

End the survey with a synthesis section covering:
- **Trends**: What directions are gaining momentum?
- **Gaps**: What problems or angles are underexplored?
- **Opportunities**: Where could a new contribution have the most impact?
- **Recommended directions**: 2-3 specific research directions worth pursuing, with brief justification.

### 7. Quality Note

Survey quality determines idea quality later. This step cannot be rushed. If the survey feels shallow, expand the search before proceeding. A thorough survey typically covers 15-30 papers.

### 8. Handoff

Save the completed survey and inform the user that the next step is `research-baseline` (selecting and specifying a baseline based on the survey results and chosen direction).
