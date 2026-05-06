---
name: research-idea
description: Generate, evaluate, and rank research ideas using structured divergent thinking and independent review.
---

Generate, evaluate, and rank research ideas using structured divergent thinking and independent review.

You are generating and evaluating research ideas for a deep learning project. This is the most critical phase — the idea determines the paper's value. Follow this process precisely.

## Input

Requires:
1. Survey results from `/research-survey`
2. Baseline specification from `/research-baseline`

If either is missing, ask the user to provide them.

**CRITICAL**: Use ONLY the survey and baseline as context. Do NOT carry over any prior rejected ideas, previous discussions, or opinions from other sessions. The idea generation context must be clean.

## Process

### Phase 1: Divergent Generation

Generate exactly **10 research ideas**. For each idea, provide:

- **Title**: Concise, descriptive name
- **Description**: 3-5 sentences explaining the core approach
- **Hypothesis**: The specific claim being tested (must be falsifiable)
- **Key Technical Insight**: What is the novel mechanism or perspective?

Guidelines for generation:
- Draw from the survey's identified gaps and opportunities
- Consider cross-domain techniques (what has worked in other areas that could transfer?)
- Think about what the baseline's weaknesses are and how to address them
- Vary the risk level: include some conservative and some ambitious ideas

### Phase 2: Independent Review

For EACH idea, evaluate in isolation. Do not let the review of one idea influence the review of another. Score each dimension 1-5:

| Dimension | Question | Scoring |
|-----------|----------|---------|
| Novelty | Is this genuinely new, or a minor variation of existing work? | 1=incremental, 5=paradigm shift |
| Feasibility | Can this be implemented and tested within a reasonable timeframe? | 1=months of engineering, 5=weeks |
| Baseline Compatibility | Does this integrate cleanly with the chosen baseline? | 1=major rewrite, 5=drop-in module |
| Complexity | Is the implementation complexity proportional to expected gains? | 1=massive effort for small gain, 5=elegant and efficient |
| Expected Impact | What magnitude of improvement is plausible? | 1=marginal, 5=substantial |

### Phase 3: Verdict

For each idea, issue one verdict:
- **GO**: Proceed to implementation. The idea is promising as-is.
- **REVISE**: The core idea has merit but needs adjustment before proceeding. Specify what to change.
- **KILL**: Not viable. Explain why (lack of novelty, infeasible, marginal expected gain).

### Phase 4: Ranking

Sort all 10 ideas by composite score (sum of 5 dimensions). Present the **top 3** with:
- Full score card
- Verdict with reasoning
- Key risk and key opportunity

### Phase 5: Prior Art Check

For the **#1 ranked idea** only:
- Do a targeted literature search specifically for this idea
- Check if similar approaches exist
- If direct prior art is found, note how this idea differs
- If the idea is killed by prior art, move to #2

### Phase 6: Recommendation

Present the recommended idea to the user with:
- Why this idea was chosen
- What the expected strengths and risks are
- What the first implementation step should be

Ask the user for approval before proceeding to `/research-contract`.

## Output Format

```markdown
# Idea Evaluation Report

## All Ideas (Ranked)

### #1: [Title]
- Scores: Novelty X | Feasibility X | Compatibility X | Complexity X | Impact X
- Total: XX/25
- Verdict: GO/REVISE/KILL
- Reasoning: ...
- Risk: ...
- Opportunity: ...

[Repeat for all 10]

## Prior Art Check (for #1)
[Search results and analysis]

## Recommendation
[Final selected idea with justification]
```

## Context Isolation Rule

The key principle: idea generation and idea review must be mentally separated. During review, pretend you are seeing each idea for the first time with no knowledge of the others. This prevents anchoring bias and ensures fair evaluation.

Save the report as `research-ideas.md`.
