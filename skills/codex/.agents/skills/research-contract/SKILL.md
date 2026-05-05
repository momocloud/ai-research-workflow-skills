---
name: research-contract
description: Trigger when user is about to start experiments and needs a formal research contract defining success/failure criteria.
---

# Research Contract

## Overview

Create a formal, immutable research contract before any experiments begin. The
contract defines exactly what constitutes success and failure, preventing post-hoc
rationalization and moving goalposts. Once experiments start, the contract is frozen.

## Prerequisites

- Selected research idea (`research-idea`) with user approval.
- Baseline specification (`research-baseline`).
- Reproduced baseline numbers (`research-reproduce`).
- All of the above in clean context.

## Process

### Step 1: Define the Hypothesis

Write a single, testable hypothesis statement:

```
Hypothesis: Applying [method/technique] to [baseline] will improve [metric] by
[threshold] on [dataset] because [mechanism].
```

The hypothesis must be:
- **Specific:** Names the method, the baseline, the metric, and the dataset.
- **Testable:** Can be confirmed or refuted by running an experiment.
- **Mechanistic:** Explains WHY the improvement should occur.

### Step 2: Method Overview

Write a concise description of the proposed method:

```
### Method Overview

[2-4 paragraphs describing:
  1. What changes are made to the baseline
  2. How the new component works
  3. Where it plugs into the existing architecture
  4. What hyperparameters it introduces]
```

### Step 3: Success Signals

Define **specific, measurable** success signals:

```
### Success Signals

S1: [Metric] on [dataset] >= [threshold] (baseline: [value])
    Rationale: [why this threshold represents meaningful improvement]

S2: [Metric] on [dataset] >= [threshold] (baseline: [value])
    Rationale: [why this secondary signal matters]

S3: [Qualitative/analytical signal, e.g., "attention maps focus on
     semantically meaningful regions"]
    Rationale: [why this supports the hypothesis]
```

Rules for success signals:
- Must be independently verifiable (another researcher could check them).
- Must have clear thresholds (not "improves" but "improves by >= 2.0 mAP").
- At least one must be a primary metric improvement.
- Include both quantitative and qualitative signals if possible.

### Step 4: Failure Signals

Define failure signals that are **independent** from "not success":

```
### Failure Signals

F1: [Metric] on [dataset] < [threshold]
    This would indicate: [what specific aspect of the hypothesis is wrong]

F2: [Metric] degrades compared to baseline
    This would indicate: [the method actively hurts performance]

F3: [Training instability / divergence / OOM]
    This would indicate: [implementation or scaling issue]
```

Failure signals are NOT just the negation of success signals. They describe
specific modes of failure and what they would reveal about the hypothesis.

### Step 5: Ablation Plan

Design ablations that isolate the contribution of each component:

```
### Ablation Plan

A1: [Remove component X]
    Expected: [metric] drops by [amount]
    This tests: [whether component X is necessary]

A2: [Simplify component Y to its simplest form]
    Expected: [metric] drops by [amount]
    This tests: [whether the complexity of Y is justified]

A3: [Vary hyperparameter Z across values: v1, v2, v3]
    Expected: [describe expected sensitivity]
    This tests: [robustness of the method to this choice]
```

### Step 6: Experimental Configuration

```
### Configuration

**Datasets:**
- Primary: [name, split, size]
- Validation: [name, split, size]

**Metrics:**
- Primary: [name, how computed]
- Secondary: [list]

**Hyperparameters:**
- Learning rate: [value]
- Batch size: [value]
- Optimizer: [name, parameters]
- Epochs/iterations: [value]
- Random seeds: [list, at least 3]
- All new hyperparameters introduced by the method: [list with values]

**Compute budget:**
- GPU type: [model]
- GPU hours: [estimate]
- Memory: [estimate]

**Data splits:**
- Train/val/test: [exact split, random seed used for splitting]
```

### Step 7: Write the Contract

Save as `research-contract-v1.md` with all sections above, plus:

```
### Metadata
- Version: 1
- Date: [date]
- Author: [user name]
- Baseline paper: [citation]
- Idea source: [idea # and title]

### Changelog
- v1: Initial contract. [date]
```

### Step 8: User Approval

Present the contract and ask the user to review and approve. This is a **critical
decision point**. The user must explicitly agree before any experiments run.

Possible feedback:
- Tighten or loosen thresholds.
- Add or remove ablations.
- Adjust hyperparameters.
- Add additional failure signals.

## Rules

- The contract is **IMMUTABLE** once experiments begin. Any change requires a new
  version (v2) with a changelog.
- Success signals must have specific numeric thresholds. "Improves performance"
  is not a valid success signal.
- Failure signals must be independent from success signals. They describe HOW
  the method could fail, not just THAT it failed.
- The ablation plan must be designed to test each claimed contribution independently.
- If the user wants to adjust the contract after seeing initial results, that is
  allowed ONLY with a version bump and changelog. The original contract and its
  verdict stand as a historical record.
- Never write a contract that guarantees success by setting trivially low thresholds.
  The contract must represent an honest assessment of what meaningful improvement
  looks like.
