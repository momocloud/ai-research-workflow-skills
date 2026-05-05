---
name: research-write
description: Trigger when user wants to write a paper draft based on completed experiments and analysis.
---

# Research Paper Writing

## Overview

Write a paper draft based on completed experiments and analysis. Every claim must
trace to a contract signal and data. All numbers must come from real runs. The
paper is a precise record of what was done, what was found, and what it means.

## Prerequisites

- Research contract (approved version).
- Experiment results (from `research-experiment`).
- Analysis (from `research-analyze`).
- All three must be in clean context.

## Process

### Step 0: Traceability Check

Before writing a single word, verify that every claim you plan to make has
provenance:

```
### Claim Traceability Matrix

| Claim | Contract Signal | Experiment Run | Log File | Line # |
|-------|----------------|----------------|----------|--------|
| "Method X improves mAP by 2.3" | S1 | main_seed1 | log.txt | L342 |
| "Component Y is essential" | A1 | ablation_a1 | a1.log | L89 |
```

If a claim cannot be traced to a specific contract signal, experiment run, and
log file, it does not go in the paper.

### Step 1: Abstract

Write the abstract LAST, but plan it first:

```
[Context: what problem and why it matters]
[Gap: what is missing in current approaches]
[Method: what we propose, in one sentence]
[Result: specific numbers against specific baselines on specific datasets]
[Impact: what this enables]
```

Rules:
- Must contain at least one specific number.
- Must name the method, the baseline, and the dataset.
- Under 250 words for conference submissions.

### Step 2: Introduction

Structure:

1. **Problem statement:** What task? Why does it matter?
2. **Current state:** What do existing methods do?
3. **Limitation:** What is the gap? (Connect to survey findings.)
4. **Our approach:** "In this paper, we propose..."
5. **Contributions:** Numbered list (typically 3-4 items). Each must be verifiable.
6. **Paper organization:** Brief roadmap.

**Rules for contributions:**
- Each contribution must be a claim that is supported by the experiments.
- "We propose X" is not a contribution. "We propose X, which achieves Y"
  is a contribution.
- Do not overclaim. If the contribution is "we show that X does not help on
  task Y," that is a valid contribution.

### Step 3: Related Work

Use the literature survey as the basis. Organize by sub-topic (same grouping
as the survey).

For each related work:
- State what they do.
- State how it differs from the proposed approach.
- Do not dismiss related work. Acknowledge its contributions honestly.

**Citation rules:**
- Only cite papers you have actually read (at least abstract + intro).
- If citing from the survey, you have already screened these.
- If you need additional citations, flag them for the user to verify.
- Never fabricate a citation.

### Step 4: Method

Structure:

1. **Problem formulation:** Formal definition of the task.
2. **Baseline overview:** Brief description of the baseline method.
3. **Proposed method:** Detailed description, organized by component.
   - Each component should map to an ablation in the contract.
4. **Training objective:** Loss function, optimization details.
5. **Complexity analysis:** Computational overhead of the proposed changes.

**Rules:**
- Be precise. Use mathematical notation where appropriate.
- Reference the contract for hyperparameter choices.
- Describe the method as implemented, not as ideally conceived.
- If the implementation deviated from the original plan, describe what was
  actually built.

### Step 5: Experiments

Structure:

1. **Setup:** Datasets, metrics, baselines, implementation details,
   compute resources.
2. **Main results:** Comparison with baseline. Table with numbers.
3. **Ablation study:** Component-by-component analysis. Table.
4. **Analysis:** Qualitative results, failure cases, discussion.

**Rules for numbers:**
- All numbers come from real experiment runs. No exceptions.
- Report mean and standard deviation across seeds.
- If a number looks wrong, investigate before publishing. Do not silently
  drop it.
- Clearly mark any result that did not meet the contract's success threshold.

### Step 6: Analysis / Discussion

This section covers:
- **Why does it work?** Mechanistic explanation grounded in ablations.
- **When does it fail?** Honest analysis of failure cases.
- **Limitations:** What the method cannot do. Be explicit.
- **Broader impact:** If applicable to the venue's requirements.

### Step 7: Conclusion

- Restate the problem and approach in one sentence.
- Summarize key findings with numbers.
- State limitations honestly.
- Suggest future directions (grounded in the failure analysis from
  `research-analyze`).

### Step 8: Reproducibility Statement

Include a dedicated section or appendix:

```
### Reproducibility

- Code: [URL or "will be released upon acceptance"]
- Datasets: [public URLs or access instructions]
- Hyperparameters: [table with all values, matching contract]
- Random seeds: [values used]
- Compute: [GPU type, count, training time]
- Total GPU hours: [number]
```

Reproducibility is non-negotiable. If any detail is missing, the paper is
incomplete.

### Step 9: Review and Iterate

After the first draft:
1. Read every claim. Verify it traces to the contract.
2. Read every number. Verify it comes from experiment logs.
3. Check that the contributions match what was actually achieved.
4. Ensure the method section describes what was actually implemented.
5. Verify all citations are real and correctly attributed.

Present the draft to the user for review.

## Rules

- Every claim traces to a contract signal + data. No orphaned claims.
- All numbers from real runs. Zero fabricated numbers.
- The paper describes what was actually done, not what was planned.
- Overclaiming is worse than underclaiming. Honest negative results are more
  valuable than inflated positive ones.
- Citations must be verified. Never invent a reference.
- The reproducibility section must be complete enough for another researcher
  to replicate the work.
- Write clearly. Prefer simple sentences over complex ones. The reader's time
  is limited.
