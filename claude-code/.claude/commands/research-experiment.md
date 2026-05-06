---
name: research-experiment
description: Implement the research idea and run experiments strictly according to the research contract.
---

Implement the research idea and run experiments strictly according to the research contract.

You are implementing the approved research idea and running all experiments. The research contract is your single source of truth.

## Input

Requires the research contract from `/research-contract`. If not available, ask the user to provide it.

Read the contract carefully before writing any code.

## Critical Rules

1. **Implement EXACTLY per the contract.** Every hyperparameter, every metric, every data split must match what the contract specifies.
2. **Report deviations IMMEDIATELY.** If you cannot follow the contract exactly (e.g., a hyperparameter causes training to diverge), stop and ask the user. Do not silently adjust parameters.
3. **Run ALL training in sub-agents or background tasks.** NEVER in the main thread. Training produces verbose output that pollutes context.
4. **On remote servers: use tmux.** Always. No exceptions.
5. **Log everything.** Training curves, checkpoints, raw metric values, random seeds, git commit hashes.
6. **Save results to a structured directory.**

## Contract Integrity Check

Before starting any experiments, lock the research contract to prevent undetected modifications:

1. Check if `.research/v1.sha256` exists:
   - **If NOT** (first run): lock the contract:
     - `mkdir -p .research/contracts`
     - `cp research-contract-v1.md .research/contracts/v1.lock.md`
     - `sha256sum research-contract-v1.md | awk '{print $1}' > .research/v1.sha256`
     - Inform the user: "Contract locked. Any modification during experiments will be detected."
     - Add `.research/` to the project's `.gitignore` if not already present.
   - **If YES** (resuming): verify the contract is intact:
     - Current hash: `sha256sum research-contract-v1.md | awk '{print $1}'`
     - Stored hash: `cat .research/v1.sha256`
     - **Match**: proceed.
     - **Mismatch**: STOP. The contract was modified after being locked. Show the diff:
       `diff research-contract-v1.md .research/contracts/v1.lock.md`
       The user must either:
       - Revert: `cp .research/contracts/v1.lock.md research-contract-v1.md`
       - Or formally upgrade to a v2 contract with explicit changelog, then re-lock.

Note: on macOS, use `shasum -a 256` instead of `sha256sum`.

## Process

### Step 1: Read the Contract

Read `research-contract-v1.md` carefully. Extract:
- Method description
- All hyperparameters with exact values
- Dataset and splits
- Metrics and thresholds
- Ablation plan

### Step 2: Plan Implementation

Before writing any code, produce an implementation plan:
- Which files from the baseline need modification
- New files/modules to create
- Data pipeline changes
- Training script modifications

Present this plan to the user for approval.

### Step 3: Implement

Write the code following the plan:
- Keep changes minimal and focused
- Preserve the baseline's structure as much as possible
- Add clear markers/comments for modified sections
- Ensure reproducibility: log all config, seeds, environment details

### Step 4: Run Main Experiment

Execute the main experiment per the contract:
- Use exact hyperparameters from the contract
- Run in sub-agent/background, tmux on remote
- Monitor training progress
- Save checkpoints and logs

### Step 5: Run Ablations

For each ablation in the contract's ablation plan:
- Implement the specific modification
- Run with the same hyperparameters (unless the ablation specifies otherwise)
- Save results separately

### Step 6: Collect Results

Gather all results into a structured format:

```
results/
  main/
    config.json
    train_log.txt
    metrics.json
    checkpoints/
  ablation_1/
    config.json
    train_log.txt
    metrics.json
  ablation_2/
    ...
```

## Output Format

Produce an experiment summary report:

```markdown
# Experiment Report

## Setup
- Git commit: [hash]
- Environment: [path to virtual env]
- GPU: [type, count]
- Start time: [timestamp]
- End time: [timestamp]

## Main Experiment Results
| Metric | Contract Target | Actual | Status |
|--------|----------------|--------|--------|
| ...    | ...            | ...    | MET/NOT MET |

## Ablation Results
| Ablation | Contract Expected | Actual | Notes |
|----------|-------------------|--------|-------|
| ...      | ...               | ...    | ...   |

## Deviations from Contract
[Any deviations, with justification]

## Issues
[Any problems encountered during the run]
```

## What NOT to Do

- Do NOT adjust hyperparameters mid-run without telling the user
- Do NOT skip ablations because "the main result is enough"
- Do NOT cherry-pick the best seed — report all runs
- Do NOT interpret results here — that's for `/research-analyze`

Save the report as `research-experiment.md`.
