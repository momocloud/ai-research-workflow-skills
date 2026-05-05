---
name: research-baseline
description: Select and specify a baseline for the research project based on survey results and research direction.
---

# Research Baseline Selection

## Trigger

Use this skill after completing `research-survey` when the user is ready to select a baseline method for their research project.

## Prerequisites

- Completed `research-survey` output
- Confirmed research direction from the user

## Instructions

### 1. Confirm Research Direction

Before selecting a baseline, confirm with the user which research direction they want to pursue from the survey's recommended directions. If the user has not reviewed the survey, ask them to do so first.

### 2. Identify Baseline Candidates

From the survey results, identify 2-3 candidate baseline papers that are:
- Directly relevant to the chosen research direction
- Representative of the current state-of-the-art on the target task
- Available with public, functional code
- Well-cited and from top venues

### 3. Baseline Specification

For each candidate, extract:

```
### [Baseline Candidate Name]
- **Paper**: [Title, Venue, Year]
- **Code**: [Link to repository]
- **Task Setting**: [What problem does this method solve?]
- **Dataset(s)**: [Standard benchmarks used]
- **Evaluation Metrics**: [Primary and secondary metrics]
- **Reported Performance**: [Key numbers from the original paper, with table references]
- **Why this baseline**: [Justification for selection]
```

### 4. Recommend a Primary Baseline

Select one primary baseline recommendation with clear justification:
- Is it the strongest existing method on the target task?
- Does it have clean, runnable code?
- Is it well-understood and widely used as a baseline in the community?
- Does it align with the user's compute budget and technical constraints?

### 5. Verify Code Accessibility

Before presenting the baseline to the user:
- Confirm the code repository exists and is accessible
- Check that it has clear setup instructions or a README
- Note the framework (PyTorch, JAX, TensorFlow) and Python version requirements
- Identify any known issues (unresolved GitHub issues, dependency problems)

### 6. User Confirmation

Present the baseline specification to the user and ask for explicit confirmation. The user should approve:
- The task setting and datasets
- The evaluation metrics
- The primary baseline choice
- The expected performance range

Do not proceed to `research-reproduce` without user sign-off.

### 7. Handoff

Once confirmed, save the baseline specification and inform the user that the next step is `research-reproduce` (reproducing the baseline results).
