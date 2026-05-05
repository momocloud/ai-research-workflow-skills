---
name: research-experiment
description: Trigger when user is ready to implement their idea and run experiments.
---

# Research Experiment Execution

## Overview

Implement the proposed method and run experiments exactly as defined in the research
contract. The contract is the single source of truth. Every deviation is recorded.
Every result is captured.

## Prerequisites

- Approved research contract (`research-contract`, version signed by user).
- Reproduced baseline (`research-reproduce`).
- Clean environment with baseline code running.
- Contract must be in clean context.

## Process

### Step 1: Read the Contract

Read the ENTIRE research contract before writing any code. Confirm understanding of:

- Hypothesis
- Method overview
- Success and failure signals
- Ablation plan
- Exact hyperparameters
- Compute budget

If anything is unclear, resolve it BEFORE proceeding. Do not implement ambiguously.

### Step 2: Plan Implementation

Create an implementation plan:

```
### Implementation Plan

1. [Component A]: [file to modify, estimated LOC]
2. [Component B]: [file to modify, estimated LOC]
3. [Training script changes]: [what changes]
4. [Evaluation script changes]: [what changes]
5. [New files to create]: [list]

Estimated implementation time: [hours]
```

Present the plan to the user for review before coding. This is NOT a critical
decision point (the user already approved the contract), but it catches
misunderstandings early.

### Step 3: Implement

Implement the method as described in the contract.

Guidelines:
- Make minimal changes to the baseline code. Isolate new components.
- Write clear docstrings explaining what each new component does.
- Add comments linking back to the contract: `# Contract: ablation A1 removes this`.
- Use configuration flags for all new components (enables easy ablation).
- Keep the implementation clean — no debug code, no commented-out alternatives.

### Step 4: Run Main Experiment

**Run in background/sub-agent. NEVER in the main thread.**

```
# On remote servers, use tmux
tmux new-session -d -s experiment 'cd /path && python train.py --config contract_v1.yaml 2>&1 | tee experiment.log'
```

1. Launch the training with ALL contract-specified hyperparameters.
2. Monitor progress: log loss, metrics, GPU usage.
3. Provide periodic updates to the user: "Epoch 10/100, loss=X.XX, metric=Y.YY".
4. If training diverges or errors occur, stop and report. Do not silently retry
   with different hyperparameters.

### Step 5: Run Ablations

After the main experiment completes, run each ablation from the contract:

```
### Ablation Execution

A1: [description] — RUNNING
  Command: python train.py --config contract_v1.yaml --disable_component_x
  Status: [PENDING / RUNNING / DONE]

A2: [description] — PENDING
  ...
```

- Run ablations sequentially or in parallel if compute allows.
- Use the SAME random seeds as the main experiment for fair comparison.
- Record all results.

### Step 6: Collect Results

Compile all results into a structured format:

```
### Experiment Results

#### Main Experiment
- Seed 1: [metric1=X, metric2=Y]
- Seed 2: [metric1=X, metric2=Y]
- Seed 3: [metric1=X, metric2=Y]
- Mean +/- Std: [computed]

#### Baseline (from reproduction)
- Mean +/- Std: [from reproduction report]

#### Comparison

| Metric   | Baseline   | Ours       | Delta      |
|----------|------------|------------|------------|
| Metric 1 | X.XX±Y.YY  | X.XX±Y.YY  | +Z.ZZ      |

#### Ablations

| Ablation     | Metric 1 | Delta from Full |
|--------------|----------|-----------------|
| Full method  | X.XX     | —               |
| A1 (no X)    | X.XX     | -Z.ZZ           |
| A2 (simple Y)| X.XX     | -Z.ZZ           |
| A3 (Z=v1)    | X.XX     | -Z.ZZ           |
```

### Step 7: Report

Present results to the user with:
1. Summary table (main result vs baseline).
2. Ablation table.
3. Any deviations from the contract (even minor ones).
4. Log file locations.
5. Checkpoint locations.

Do NOT interpret the results at this stage. That is the job of `research-analyze`.

## Rules

- The contract is the **single source of truth**. Implement EXACTLY what it says.
- Report ANY deviation from the contract immediately. Common deviations:
  - Different hyperparameter (even slightly different learning rate).
  - Different data split.
  - OOM requiring smaller batch size.
  - Bug fix that changes behavior.
- ALL training runs execute in background/sub-agent.
- ALL logs go to files, never to main context.
- NEVER silently retry with different hyperparameters to "make it work."
- NEVER modify the contract to match results. If results don't meet the contract,
  that is an honest finding.
- Use EXACTLY the random seeds specified in the contract.
- If training fails (divergence, NaN, OOM), stop and report. Do not attempt to
  fix it autonomously — involve the user.
