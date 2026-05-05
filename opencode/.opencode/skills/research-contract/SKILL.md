---
name: research-contract
description: Write a formal research contract defining hypothesis, success/failure signals, and ablation plan before experiments begin.
---

# Research Contract

## Trigger

Use this skill after `research-idea` when the user has selected a research idea and needs to formalize the experiment plan.

## Prerequisites

- Completed `research-idea` output with user-selected idea
- Completed `research-baseline` specification
- Completed `research-reproduce` results

## Instructions

### 1. Contract Philosophy

The research contract is the **single source of truth** for evaluating experiments. It defines what success and failure look like **before** any code is written. This prevents retroactive goalpost moving -- the most common failure mode in AI-assisted research.

**The contract is IMMUTABLE once experiments begin.** Changes require creating a new version (v2, v3) with an explicit changelog.

### 2. Contract Structure

Write the contract with the following sections:

```markdown
# Research Contract v1

## 1. Hypothesis

[One clear, testable statement. Format: "We hypothesize that [method] will
improve [metric] on [task] because [mechanism]."]

## 2. Method Overview

[2-3 paragraph description of the proposed method. Include:
- What changes are made to the baseline
- Key algorithmic components
- Why each component is expected to help]

## 3. Success Signals

[Specific, measurable criteria. Each signal must have a threshold.]

| Signal | Metric | Threshold | Rationale |
|--------|--------|-----------|-----------|
| Primary | [e.g., mAP on COCO] | [e.g., +2.0 over baseline] | [Why this is meaningful] |
| Secondary | [e.g., inference speed] | [e.g., < 5% slower] | [Why this matters] |

## 4. Failure Signals

[Independent from "not success." What specific outcomes would indicate the
approach is fundamentally flawed, not just underperforming?]

| Signal | Metric | Threshold | Interpretation |
|--------|--------|-----------|---------------|
| [e.g., Training collapse] | [Loss diverges] | [Loss > 10x baseline at any epoch] | [Method is unstable] |
| [e.g., No improvement trend] | [Metric delta] | [No improvement by epoch 50] | [Core mechanism ineffective] |

## 5. Ablation Plan

[Each ablation tests a specific component of the method.]

| Ablation | What is removed/changed | Expected outcome | What it validates |
|----------|------------------------|------------------|-------------------|
| [Name] | [Description] | [Expected metric change] | [What this proves] |

## 6. Experimental Setup

### Metrics
- Primary: [metric name, how computed]
- Secondary: [metric name, how computed]

### Data
- Dataset: [name, version, splits]
- Train/Val/Test split: [exact split strategy]
- Preprocessing: [any special handling]

### Hyperparameters
| Parameter | Value | Source |
|-----------|-------|--------|
| Learning rate | [value] | [baseline default / tuned] |
| Batch size | [value] | [baseline default] |

### Compute Budget
- Hardware: [GPU type, count]
- Expected training time: [per run]
- Total runs planned: [main + ablations]
- Total compute estimate: [GPU-hours]

## 7. Reproducibility
- Random seeds: [specify, e.g., 42, 123, 456]
- Number of runs per configuration: [e.g., 3]
- Reporting: [mean +/- std]
```

### 3. Success Signals Must Be Specific

Vague success signals defeat the purpose of the contract. Bad examples:
- "Improved performance" (how much? on what?)
- "Competitive with SOTA" (what does competitive mean?)

Good examples:
- "mAP improves by at least +1.5 points on COCO val2017"
- "Training time increases by no more than 10%"
- "The method generalizes: improvement holds on at least 2 of 3 out-of-distribution test sets"

### 4. Failure Signals Must Be Independent

Failure signals are not simply "success signals not met." They should capture **orthogonal evidence that the approach is flawed.** Examples:
- Loss diverges or becomes unstable during training
- Performance degrades on easy examples while improving on hard ones (indicating a trade-off, not an improvement)
- Ablation shows no component contributes meaningfully (indicating the improvement is from confounding factors)

### 5. User Approval

Present the contract to the user for approval. The user should verify:
- The hypothesis is correctly captured
- Success thresholds are meaningful and achievable
- Failure signals catch the right failure modes
- The compute budget is acceptable
- The ablation plan covers the key components

**Do not proceed to `research-experiment` without explicit user approval of the contract.**

### 6. Save Contract

Save the approved contract as `research-contract-v1.md` in the project directory.

### 7. Handoff

Inform the user that the next step is `research-experiment` (implementing and running experiments according to the contract).
