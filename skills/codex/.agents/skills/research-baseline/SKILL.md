---
name: research-baseline
description: Trigger when user needs to select a baseline for their research project.
---

# Research Baseline Selection

## Overview

Select a concrete baseline for the research project. The baseline anchors all
subsequent work — experiments are measured against it, and ideas are judged by
whether they can improve upon it. Getting this right is critical.

## Prerequisites

- Completed literature survey (`research-survey`).
- Survey results must be in context (clean, not polluted by other tasks).

## Process

### Step 1: Define the Task Setting

From the survey results and user discussion, pin down:

- **Task:** What exactly is being solved? (e.g., "image segmentation on medical
  images," not just "segmentation")
- **Dataset(s):** What benchmark(s) will be used? Identify the standard ones
  for this task from the survey.
- **Evaluation metrics:** What metrics does the community use? Identify the
  primary metric and any secondary metrics.
- **Constraints:** Compute budget, data access, time constraints.

### Step 2: Identify Candidate Baselines

From the survey, identify 2-4 candidate baseline papers. For each candidate:

- Is there open-source code available?
- Is the code actively maintained (recent commits, open issues)?
- Does the paper report results on the target dataset(s)?
- Is the method well-understood and widely cited?
- Can it be reproduced with reasonable compute?

### Step 3: Select Primary Baseline

Choose ONE primary baseline. Justify the choice with:

1. **Relevance:** Directly addresses the same task setting.
2. **Reproducibility:** Has clean, working code.
3. **Performance:** Represents a strong but not SOTA result (SOTA baselines
   leave less room for improvement; weak baselines are unfair comparisons).
4. **Community standing:** Widely cited and used as a baseline by others.
5. **Code quality:** Well-documented, reasonable dependencies.

### Step 4: Compile Baseline Specification

Output a structured baseline spec:

```
## Baseline Specification

### Task Setting
- Task: [precise description]
- Datasets: [list with splits]
- Primary metric: [name]
- Secondary metrics: [list]

### Primary Baseline
- Paper: [Title, Authors, Venue, Year]
- Code: [URL]
- Expected performance (from paper):
  - Dataset A: [metric] = [value]
  - Dataset B: [metric] = [value]
- Justification: [why this baseline]

### Environment Notes
- Framework: [PyTorch/TensorFlow/JAX]
- GPU requirements: [estimated]
- Training time: [estimated from paper]

### Risks
- [Any concerns about reproducibility, data access, etc.]
```

### Step 5: Confirm with User

Present the baseline spec and ask the user to confirm or adjust. This is a
**critical decision point** — do not proceed without explicit approval.

Possible adjustments:
- User prefers a different baseline from the candidates.
- User wants to add a second baseline for comparison.
- User wants to use a different dataset or metric.

## Rules

- The baseline MUST have open-source code. No exceptions.
- The baseline MUST report results on the target dataset(s) in the original paper.
- Do not select a baseline you cannot reproduce.
- If no suitable baseline exists, report this honestly and suggest alternatives
  (e.g., broadening the task, using a related task's baseline, implementing a
  simple method from scratch).
- Never fabricate expected performance numbers. Only use values reported in the
  original paper.
