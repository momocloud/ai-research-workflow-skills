---
name: research-experiment
description: Implement the research idea and run experiments strictly according to the research contract.
---

# Research Experiment

## Trigger

Use this skill after `research-contract` when the user has approved the research contract and is ready to implement and run experiments.

## Prerequisites

- Approved `research-contract` (v1 or later)
- Completed `research-reproduce` (verified baseline code and environment)

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

## Instructions

### 1. The Contract Is the Single Source of Truth

Every implementation decision references the contract. If the contract says "learning rate 1e-4," the learning rate is 1e-4. If the contract says "3 ablations," 3 ablations are run.

- Implement **EXACTLY** per the contract.
- Report any deviations immediately -- do not silently adjust.
- If something in the contract seems wrong, stop and discuss with the user before deviating.

### 2. Implementation Process

Follow this sequence:

```
Read contract -> Plan implementation -> Implement -> Run main experiment -> Run ablations -> Collect results
```

#### 2a. Read Contract

Load the approved contract. Parse out:
- Method description (what to implement)
- Hyperparameters (exact values)
- Metrics (what to track)
- Ablation plan (what to run after the main experiment)
- Success/failure signals (for later analysis)

#### 2b. Plan Implementation

Before writing code, produce a brief implementation plan:
- Which files in the baseline codebase will be modified
- What new modules/files will be created
- How the method maps to code changes
- Any dependencies or prerequisites

Present this plan to the user for a quick sanity check before coding.

#### 2c. Implement

Write the code with these principles:
- Minimize changes to the baseline codebase. Prefer adding new modules over modifying existing ones.
- Keep changes traceable: each contract component should map to specific code.
- Add clear comments linking code sections back to contract sections.
- Write code that logs all metrics specified in the contract.

### 3. Execution Rules

**All training and evaluation runs execute in background:**
- Use sub-agent calls or background processes
- Use `tmux` on remote servers
- Never block the main conversation thread with training runs

**Monitor progress with lightweight check-ins:**
- Log epoch-level metrics to a file
- Check progress periodically (not by streaming logs)
- Alert the user if training shows signs of instability early

**Run order:**
1. Main experiment (full method)
2. Ablation experiments (one at a time, per the ablation plan)
3. Collect all results into a structured format

### 4. Logging Requirements

Log the following for every run:
- Full configuration (hyperparameters, code commit hash)
- All metrics at every evaluation checkpoint
- Training curves (loss, primary metric over time)
- Hardware and timing information
- Any errors or warnings encountered

Save logs to organized directories:
```
experiments/
  main-run/
    config.json
    metrics.jsonl
    training_log.txt
  ablation-[name]/
    config.json
    metrics.jsonl
    training_log.txt
```

### 5. Handling Deviations

If something deviates from the contract during implementation or execution:

1. **Stop.** Do not silently adjust.
2. **Document** the deviation clearly: what changed, why, and what impact it might have.
3. **Report** to the user immediately.
4. **Decide** with the user: fix the implementation to match the contract, or create a contract amendment (v2).

### 6. Output

Produce a structured results file:

```markdown
## Experiment Results

### Main Experiment
- **Config**: [Summary matching contract]
- **Results**:
  | Metric | Contract Target | Achieved | Status |
  |--------|----------------|----------|--------|
  | [Metric 1] | [Target] | [Value] | [MET/NOT MET] |
  | [Metric 2] | [Target] | [Value] | [MET/NOT MET] |

### Ablation Results
- **[Ablation 1 name]**: [Result, compared to main experiment]
- **[Ablation 2 name]**: [Result, compared to main experiment]

### Deviations from Contract
- [None, or list with explanations]

### Raw Results Location
- Main run: [path]
- Ablations: [path(s)]
```

### 7. Handoff

Save the results and inform the user that the next step is `research-analyze` (evaluating results against the contract with signal-by-signal assessment).
