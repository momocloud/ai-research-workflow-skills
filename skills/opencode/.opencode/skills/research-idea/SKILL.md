---
name: research-idea
description: Generate, evaluate, and rank research ideas using structured divergent thinking and independent review.
---

# Research Idea Generation

## Trigger

Use this skill after completing `research-survey` and `research-reproduce` when the user is ready to brainstorm and select a research idea.

## Prerequisites

- Completed `research-survey` output (clean summary, not raw conversation)
- Completed `research-reproduce` output (reproduction report with verified baseline numbers)
- Clean context -- do not carry raw logs or full paper texts into this skill

## Instructions

### 1. Prepare Context

Load only the structured outputs from prior skills:
- Survey synthesis (trends, gaps, opportunities)
- Baseline specification (task, datasets, metrics, code)
- Reproduction results (verified baseline numbers, observations)

Do NOT load raw training logs, full paper texts, or debugging history. Context pollution at this stage will degrade idea quality.

### 2. Generate Ideas (Divergent Phase)

Generate exactly **10 research ideas**. For each idea, provide:

```
### Idea #[N]: [Title]

**Description**: [2-3 sentences explaining the approach]

**Hypothesis**: [Testable hypothesis -- what specific improvement do you expect?]

**Key Insight**: [1 sentence: why might this work when existing methods don't?]

**Connection to baseline**: [How does this modify or extend the baseline?]
```

Guidelines for generation:
- Ideas should range from conservative (small modification, high confidence) to ambitious (novel approach, higher risk).
- Each idea must be implementable on top of or alongside the chosen baseline.
- Ideas should be grounded in observations from the survey and reproduction, not pure speculation.
- Include at least 2 ideas that challenge assumptions in the field.

### 3. Review Ideas (Independent Evaluation)

Now switch to a critical reviewer mindset. Evaluate each idea independently on five dimensions (1-5 scale):

| Dimension | 1 (Low) | 5 (High) |
|-----------|---------|----------|
| **Novelty** | Incremental extension | Fundamentally new approach |
| **Feasibility** | Major engineering challenge | Straightforward to implement |
| **Baseline Compatibility** | Requires new pipeline | Drops into existing code |
| **Complexity** | Many moving parts | Clean, minimal change |
| **Expected Impact** | Marginal improvement | Significant advance |

**Context isolation between generation and review is essential.** Generate all 10 ideas first, then review them. Do not review-as-you-go, as this biases later ideas.

### 4. Verdict

For each idea, assign a verdict:
- **GO**: Strong candidate. Proceed to detailed consideration.
- **REVISE**: Promising direction but needs refinement before it can be evaluated.
- **KILL**: Significant flaw (low novelty, infeasible, or incompatible with baseline).

Provide 1-2 sentences of reasoning for each verdict.

### 5. Rank and Present

- Rank all GO ideas by their total score (sum of 5 dimensions).
- Present the **top 3** to the user with full evaluation details.
- Include a brief comparison table.

### 6. Prior Art Check

For the **number 1 ranked idea**, perform a targeted prior art check:
- Search for existing work that is very similar.
- If highly similar work exists, note it and suggest how to differentiate.
- If the idea survives the prior art check, recommend it.

### 7. User Decision

Present the ranked ideas with prior art check results. Ask the user to select one idea (or request modifications). The user may:
- Select the recommended idea
- Choose a different ranked idea
- Request modifications to an idea
- Request a new round of generation

Do not proceed to `research-contract` without explicit user selection.

### 8. Handoff

Once the user selects an idea, save a structured idea specification and inform the user that the next step is `research-contract` (formalizing the hypothesis and success criteria).
