---
name: research-reproduce
description: Trigger when user needs to reproduce a baseline paper's results.
---

# Research Baseline Reproduction

## Overview

Reproduce the baseline paper's reported results. This validates the codebase,
confirms the experimental setup, and establishes the numbers against which all
future improvements will be measured. Reproducibility is a prerequisite for
credible research.

## Prerequisites

- Completed baseline specification (`research-baseline`).
- Baseline spec must be in clean context.

## Process

### Step 1: Environment Setup

**CRITICAL: ALL setup must run in a sub-agent or background process. NEVER in
the main thread.**

1. Read any existing documentation about data paths, environment variables, and
   setup instructions BEFORE downloading anything.
2. Clone the repository.
3. Install dependencies (pip/conda) in background.
4. Download datasets in background.
5. Verify dataset integrity after download (checksums, file counts, sample
   inspection).

**On remote servers:** Use `tmux` for all long-running operations. Provide the
user with the tmux session name so they can monitor progress.

```
# Example pattern for remote servers
tmux new-session -d -s reproduce 'cd /path/to/repo && bash setup.sh 2>&1 | tee setup.log'
```

### Step 2: Configuration

1. Locate the config files or command-line arguments for the target experiment.
2. Identify the exact configuration used to produce the reported numbers.
3. Note any missing configuration details (contact authors if critical).
4. Adjust data paths, GPU assignments, and other environment-specific settings.

### Step 3: Training Run

**Run training in background. NEVER in the main thread.**

1. Launch the training command in a sub-agent or tmux session.
2. Monitor progress with periodic checks (loss curves, epoch progress).
3. Provide real-time feedback to the user: "Epoch 5/100, loss=X.XX, ETA=Yh".
4. Capture all logs to files. Do NOT stream training logs into the main context.

### Step 4: Evaluation

1. Run the official evaluation script on the trained model.
2. Record all metrics reported in the original paper.
3. Compare reproduced numbers against reported numbers.

### Step 5: Reproduction Report

Output a structured reproduction report:

```
## Reproduction Report

### Setup
- Repository: [URL, commit hash]
- Environment: [OS, Python, PyTorch/TensorFlow version, CUDA version]
- Hardware: [GPU model, count]
- Dataset: [name, version, path]
- Configuration: [any changes from default]

### Results Comparison

| Metric        | Reported | Reproduced | Delta   |
|---------------|----------|------------|---------|
| Metric 1      | X.XX     | X.XX       | +/-X.XX |
| Metric 2      | X.XX     | X.XX       | +/-X.XX |

### Environment Paths
- Code: [absolute path]
- Data: [absolute path]
- Checkpoints: [absolute path]
- Logs: [absolute path]

### Deviations
- [Any deviations from the original setup, even minor ones]

### Time and Resources
- Setup time: [duration]
- Training time: [duration]
- Peak GPU memory: [value]
- Disk usage: [value]

### Assessment
- [PASS/PARTIAL/FAIL with explanation]
```

### Step 6: Handle Problems

If reproduction is problematic:
- **Missing data:** Check if the dataset is publicly available. If not, suggest
  alternatives or contact authors.
- **OOM errors:** Suggest reducing batch size, gradient accumulation, or mixed
  precision.
- **Divergent training:** Check learning rate, data preprocessing, random seeds.
- **Code errors:** Check issues on GitHub. Try a different commit.

**When stuck, involve the user.** Human-in-the-loop is the most reliable path
for debugging reproduction issues. Do not spend more than 2 iterations of
autonomous debugging before escalating.

## Rules

- ALL setup, training, and evaluation runs in background/sub-agent.
- ALL logs go to files, never to main context.
- ALWAYS verify datasets after download (file count, sample inspection).
- ALWAYS read existing data path docs BEFORE downloading.
- NEVER modify the baseline code unless absolutely necessary. If modified,
  document the change precisely.
- NEVER fabricate reproduction numbers. If the numbers don't match, report
  honestly.
- NEVER run pip install / conda install in the main thread.
- If a problem requires more than basic debugging, escalate to the user rather
  than burning context on repeated failures.
