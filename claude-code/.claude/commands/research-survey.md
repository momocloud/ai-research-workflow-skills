Literature survey for a deep learning research topic. Searches for relevant top-venue papers, summarizes findings, and identifies trends and opportunities.

You are conducting a comprehensive literature survey for a deep learning research project. Follow these steps precisely.

## Input

Ask the user for their research topic or direction if not already provided. Be specific — ask for:
- The general area (e.g., "object detection", "sequence modeling")
- Any specific problem they want to address
- Any constraints (specific dataset, computational budget, etc.)

## Search Strategy

1. Use web search tools to find relevant papers. If Gemini/Grounding search is available, prefer it — Gemini has the strongest search capability among current models.
2. Prioritize papers with these characteristics:
   - Published at top venues: CVPR, ICCV, ECCV, NeurIPS, ICML, ICLR, ACL, EMNLP, AAAI, IJCAI
   - Has open-source code (GitHub or similar)
   - Published within the last 3 years (older seminal works are exceptions)
3. For each paper found: prefer LaTeX source or Markdown over PDF when possible. arXiv papers almost always have LaTeX source available. Models do not truly read PDFs end-to-end — they skim. With structured source, you can feed only relevant sections.
4. At this survey stage, only read **abstracts and introductions**. Do not dive into experiment details or method specifics yet. You are judging relevance, not doing deep analysis.

## Output Format

Produce a structured survey document with the following for each paper:

| Field | Content |
|-------|---------|
| Title | Full paper title |
| Venue + Year | e.g., "CVPR 2025" |
| Code | Yes/No + link if available |
| Summary | 2-3 sentences capturing the core contribution |
| Relevance | High / Medium / Low (to the user's topic) |
| Key Insight | One sentence: what is the most important takeaway |

Group papers by sub-topic or approach. Use headers like `### Sub-topic: [name]`.

## Synthesis

End the survey with a synthesis section:
1. **Current trends**: What approaches are dominant? What is the community converging on?
2. **Gaps**: What problems remain unsolved? What settings are underexplored?
3. **Opportunities**: Based on the gaps, what directions look promising?
4. **Recommended baseline candidates**: Which 2-3 papers would be the strongest candidates for baseline? Consider: code availability, reproducibility, how recently published, how well it represents the current SOTA.

## Critical Principle

Literature survey builds domain-expert-level context. The quality of ideas generated later is directly proportional to the quality of this survey. Garbage in, garbage out. If the survey only finds tangentially related papers, the subsequent ideas will be equally shallow. Take the time to search thoroughly from multiple angles.

Save the survey document as `research-survey.md` in the project directory.
