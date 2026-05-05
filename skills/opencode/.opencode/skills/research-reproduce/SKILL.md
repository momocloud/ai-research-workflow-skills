---
name: research-reproduce
description: Reproduce baseline paper results with strict context isolation and progress monitoring.
---

# Research Baseline Reproduction

## Trigger

Use this skill after `research-baseline` when the user has confirmed a baseline and is ready to reproduce its results.

## Prerequisites

- Completed `research-baseline` output with user confirmation
- Access to the baseline code repository

## Instructions

### 1. Pre-Flight Checks

Before writing any code:

- **Read existing data path documentation** in the repository before downloading datasets. Many repos have specific instructions for data setup.
- Check if there are pre-trained model checkpoints available for evaluation-only reproduction.
- Identify the compute requirements (GPU type, memory, expected training time).
- Note the exact commit hash or version tag to use.

### 2. Context Isolation Rules

This skill involves heavy dirty work. Follow these rules strictly:

- **ALL setup, installation, and training runs MUST execute in background processes or sub-agents** -- NEVER in the main conversation thread.
- On remote servers, use `tmux` sessions so processes survive disconnections.
- Isolate logs from the main context. Do not paste raw training logs into the main thread.
- Extract only summary metrics and key observations for the main thread.
- If a step fails, diagnose in the sub-agent, then report only the diagnosis and fix to the main thread.

### 3. Setup Process

Execute in order (each in background):

1. **Clone repository** at the specified commit/tag.
2. **Create virtual environment** and install dependencies. Never run `pip install` in the main thread. If dependency conflicts arise, note them but do not debug in the main thread.
3. **Download datasets** following the repository's instructions. Verify dataset integrity after download (file counts, sizes, checksums if provided). Record exact data paths for later use.
4. **Verify environment** by running any provided sanity checks or unit tests.

### 4. Training / Evaluation

- Run the baseline training or evaluation script as specified in the repository documentation.
- Use the exact hyperparameters from the original paper (or repository defaults if they match).
- Monitor progress with lightweight check-ins (e.g., epoch-level metrics), not full log streaming.
- If training is long-running, set up periodic checkpoint saves.

### 5. Handling Problems

If reproduction encounters significant issues:
- Do not endlessly debug in the main thread.
- Summarize the problem clearly and present it to the user.
- When setup is problematic, human-in-the-loop is the most reliable approach.
- Consider alternative approaches: using pre-trained checkpoints, switching to a different baseline, or using a verified reproduction from another source.

### 6. Output

Produce a **Reproduction Report**:

```
## Reproduction Report

### Environment
- **Repository**: [URL, commit hash]
- **Framework**: [PyTorch/JAX/TF version]
- **Python**: [Version]
- **Hardware**: [GPU type, count]
- **Dataset paths**: [Exact paths used]

### Configuration
- **Hyperparameters**: [Table matching paper's setup]
- **Training duration**: [Actual time, epochs, iterations]
- **Any deviations from paper**: [List, or "None"]

### Results
- **Metric 1**: [Reproduced] vs [Paper reported]
- **Metric 2**: [Reproduced] vs [Paper reported]
- **Gap analysis**: [Explanation of any differences]

### Observations
- [Any notable findings during reproduction]

### Clean Environment Paths
- Code: [path]
- Data: [path]
- Checkpoints: [path]
- Logs: [path]
```

### 7. Handoff

Save the reproduction report and inform the user that the next step is `research-idea` (generating research ideas based on survey and baseline understanding).
