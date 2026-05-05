---
name: research-idea
description: Trigger when user wants to generate and evaluate research ideas. Use for brainstorming and ranking potential approaches.
---

# Research Idea Generation and Evaluation

## Overview

Generate a broad set of research ideas, evaluate each independently, and rank them
to select the most promising direction. Context isolation between generation and
evaluation is essential — evaluation must not be biased by generation enthusiasm.

## Prerequisites

- Completed literature survey (`research-survey`).
- Selected baseline (`research-baseline`).
- Both survey results and baseline spec must be in clean context.
- If context is polluted from previous tasks, reset before starting.

## Process

### Step 1: Generate Ideas (Divergent Phase)

Generate **10 ideas**. At this stage, be creative and expansive. Do not self-censor.

For each idea, write:

```
### Idea #[N]: [Title]

**Description:** [2-3 sentences explaining the approach]

**Hypothesis:** [Testable hypothesis — "If we do X, then Y will improve because Z"]

**Key insight:** [One sentence: what is the novel observation or mechanism?]

**Connection to baseline:** [How does this modify/extend the baseline?]
```

Sources of inspiration:
- Gaps identified in the survey synthesis.
- Limitations of the baseline method.
- Recent techniques from adjacent areas that haven't been applied to this task.
- Failure modes of current methods.
- Ablation insights from the baseline paper (what did they not try?).

### Step 2: Evaluate Ideas (Convergent Phase)

**CRITICAL: Context isolation required.** Evaluate each idea independently. Do not
carry enthusiasm from generation into evaluation. Be ruthlessly honest.

Score each idea on 5 dimensions (1-5 scale):

| Dimension        | 1 (Poor)                | 5 (Excellent)           |
|------------------|--------------------------|--------------------------|
| **Novelty**      | Incremental tweak        | New mechanism/perspective |
| **Feasibility**  | Requires unavailable data| Implementable in days    |
| **Baseline Fit** | Requires rearchitecting  | Clean extension          |
| **Complexity**   | Many moving parts        | Minimal changes          |
| **Impact**       | Marginal improvement     | Significant advance      |

For each idea, write:

```
### Evaluation: Idea #[N]

| Dimension       | Score | Justification |
|-----------------|-------|---------------|
| Novelty         | X/5   | ...           |
| Feasibility     | X/5   | ...           |
| Baseline Fit    | X/5   | ...           |
| Complexity      | X/5   | ...           |
| Impact          | X/5   | ...           |
| **Total**       | **X/25** |             |

**Verdict: GO / REVISE / KILL**
**Reasoning:** [2-3 sentences]
```

**Verdict criteria:**
- **GO:** Total >= 18, no dimension below 3.
- **REVISE:** Total 14-17, or one dimension at 2. Salvageable with changes.
- **KILL:** Total < 14, or any dimension at 1. Not worth pursuing.

### Step 3: Rank and Present Top 3

Rank all ideas by total score. Present the top 3 with:
- Summary (title + key insight)
- Total score
- Verdict
- Main strength
- Main risk

### Step 4: Prior Art Check for #1

For the top-ranked idea:
- Search for existing work that has tried the same or very similar approach.
- Check if the key mechanism has been explored in a different context.
- If prior art exists, assess whether the proposed application is sufficiently
  different to be novel.

Output:
```
### Prior Art Check: Idea #[N]

- [Prior work 1]: [how it relates, key difference]
- [Prior work 2]: [how it relates, key difference]
- ...

**Novelty assessment:** [Still novel / Partially anticipated / Already done]
```

### Step 5: Recommend to User

Present a clear recommendation:
- **Recommended idea:** #N — [title]
- **Why:** [2-3 sentences]
- **Main risk:** [honest assessment]
- **Fallback:** #M — [title] if the recommended idea fails

Ask the user to confirm or select a different idea. This is a **critical decision
point** — do not proceed without explicit approval.

## Rules

- Generate exactly 10 ideas. Do not stop early because you think you have "enough."
- Evaluate ALL 10 ideas. Do not skip evaluation for obviously bad ones.
- Context isolation between generation and evaluation is non-negotiable. If you
  find yourself being generous in evaluation, reset and re-evaluate.
- Be honest in evaluation. A KILL verdict is valuable — it saves time.
- The prior art check must be thorough. Discovering your idea is already published
  at this stage is far better than discovering it at submission time.
- Never propose an idea that requires unavailable data or compute beyond the
  stated budget.
