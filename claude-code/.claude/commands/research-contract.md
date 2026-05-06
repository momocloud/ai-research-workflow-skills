---
name: research-contract
description: Write a formal research contract defining hypothesis, success/failure signals, and ablation plan before any experiments begin.
---

Write a formal research contract defining hypothesis, success/failure signals, and ablation plan before any experiments begin.

You are writing a Research Contract — the most important document in this workflow. This contract defines what constitutes success and failure BEFORE any experiment runs. Its purpose is to prevent post-hoc rationalization.

## Input

Requires:
1. Selected idea from `/research-idea`
2. Baseline specification from `/research-baseline`

If either is missing, ask the user to provide them.

## What Goes Wrong Without This

Without a contract, the model WILL rationalize results after the fact:
- "Although the main metric didn't improve, we observe interesting patterns..."
- "The method shows promise in a different setting..."
- "Upon closer analysis, the failure reveals important insights..."

These are post-hoc stories. The contract prevents this by defining criteria BEFORE results exist.

## Contract Structure

Write the contract with these sections:

```markdown
# Research Contract v1

## Hypothesis
[One clear, falsifiable statement. Example: "Adding attention-guided feature fusion to the
baseline's backbone will improve mAP@0.5 by at least 2% on the COCO dataset, because it
allows the model to selectively emphasize discriminative regions."]

## Method Overview
[High-level description of the proposed approach. Specific enough that someone could
re-implement from this description alone. Include:
- What module/component is being added or modified
- Where it plugs into the baseline architecture
- Key hyperparameters and their default values]

## Success Signals
[Specific, measurable criteria. Each signal must be independently verifiable.]
1. [e.g., "mAP@0.5 improves by ≥2% over baseline on COCO val2017"]
2. [e.g., "Improvement is consistent across at least 2 different backbone architectures"]
3. [e.g., "Inference time increases by no more than 15%"]

## Failure Signals
[Independent criteria for failure. NOT just "didn't meet success" — these must be
separately defined negative outcomes.]
1. [e.g., "mAP@0.5 degrades or improves by <0.5%"]
2. [e.g., "Method shows inconsistent behavior across different random seeds (std > 1%)"]
3. [e.g., "Training becomes unstable (loss diverges in >30% of runs)"]

## Ablation Plan
| # | Ablation | What It Tests | Expected Outcome |
|---|----------|---------------|------------------|
| 1 | [e.g., "Remove attention, use uniform weights"] | [e.g., "Whether learned attention matters"] | [e.g., "Performance drops by 1-2%"] |
| 2 | ... | ... | ... |

## Metrics
- Primary metric: [name, threshold for success]
- Secondary metrics: [names]
- Statistical significance: [e.g., "mean ± std over 3 runs with different seeds"]

## Data
- Dataset: [name, version]
- Splits: [train/val/test specifics]
- Preprocessing: [any special handling]
- Expected dataset size after preprocessing: [number of samples]

## Hyperparameters
- Learning rate: [value or range]
- Batch size: [value]
- Optimizer: [name, momentum, weight decay]
- LR schedule: [type, milestones]
- Epochs/iterations: [number]
- Any method-specific hyperparameters: [with defaults and rationale]

## Compute Budget
- GPU type: [e.g., "A100 80GB"]
- Expected time per run: [e.g., "~12 hours"]
- Total budget: [e.g., "~72 hours (6 runs including ablations)"]
- Max allowed runtime: [hard cutoff]

## Implementation Notes
[Any constraints on the implementation: which files to modify, framework version, etc.]
```

## Rules

1. **Contract is IMMUTABLE** once experiments begin.
2. If changes are needed during experiments, produce a **v2** with:
   - Explicit changelog ("Changed X from Y to Z because...")
   - Reason for the change
   - Which results are affected
3. **Never retroactively adjust success criteria** to match observed results.
4. All subsequent phases (experiment, analyze, write) treat this contract as the single source of truth.

Save the contract as `research-contract-v1.md`.
