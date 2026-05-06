---
name: research-reproduce
description: Reproduce the baseline paper's results with strict context isolation and progress monitoring.
---

Reproduce the baseline paper's results with strict context isolation and progress monitoring.

You are reproducing a baseline paper's results. This is one of the most underestimated steps in the research workflow. The reproduction serves as the anchor for ALL future experiments — your idea's improvement is only meaningful when measured against a verified baseline.

## Input

Requires the baseline specification from `/research-baseline`. If not available, ask the user to provide:
- Paper title and code repository
- Expected performance numbers
- Dataset details

## Critical Rules — Read Before Proceeding

1. **NEVER run setup, installation, or training in the main thread.** Use sub-agents or `run_in_background` for ALL bash commands that take more than 30 seconds.
2. **On remote servers: ALWAYS use tmux.** Never run long processes over bare SSH. One disconnection kills everything.
3. **Isolate logs from main context.** Installation output, download progress, and error traces are noise — they degrade the quality of subsequent reasoning. Run dirty work in sub-agents; only bring back clean results.
4. **Read existing documentation FIRST.** Before downloading datasets or setting up environments, check for any existing docs about data paths and environment configuration. Do not assume you need to start from scratch.
5. **Monitor progress with real-time feedback.** Do not use sleep/wait loops silently. If a process is stuck for more than 2 minutes, alert immediately.
6. **Verify datasets after download.** Check file sizes, checksums, or sample integrity. Do not proceed with training on potentially corrupted data.

## Process

1. **Environment Setup** (in sub-agent/background):
   - Clone the code repository
   - Create virtual environment
   - Install dependencies
   - Report any failures immediately

2. **Data Preparation** (in sub-agent/background):
   - Check for existing data at known paths
   - Download if necessary, with progress monitoring
   - Verify data integrity

3. **Training** (in sub-agent/background, tmux on remote):
   - Run the training script with default/recommended settings
   - Monitor training progress periodically
   - Log all outputs

4. **Evaluation**:
   - Run evaluation on the trained checkpoint
   - Compare results with reported baseline numbers

## Output Format

Produce a reproduction report:

```markdown
# Baseline Reproduction Report

## Environment
- Python version:
- PyTorch/TF version:
- CUDA version:
- GPU:
- Virtual env path:
- Data path:

## Results
| Metric | Reported | Reproduced | Delta |
|--------|----------|------------|-------|
| ...    | ...      | ...        | ...   |

## Deviations
[Any differences from the original paper's setup or results]

## Issues Encountered
[Problems during setup, training, or evaluation]

## Verified Clean Paths
- Code: [path]
- Data: [path]
- Checkpoints: [path]
- Logs: [path]
```

## Important Warning

If environment setup is consistently failing (network issues, dependency conflicts, etc.), the most reliable approach is:
1. **Stop** and inform the user
2. Let the user set up the environment manually (human-in-the-loop)
3. Once environment is ready, start a NEW session with clean context
4. Provide the clean paths directly to the model

Do not burn hours of context trying to fix environment issues. This is the #1 context pollution source.

Save the report as `research-reproduce.md`.
