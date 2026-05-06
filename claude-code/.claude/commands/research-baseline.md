Determine the baseline for the research project based on survey results and research direction.

You are selecting and specifying a baseline for a deep learning research project. Follow these steps precisely.

## Input

Requires the output from `/research-survey`. If the survey document is not available:
- Ask the user to provide it, OR
- Ask the user for their research direction and run a targeted survey first

## Selection Process

Based on the survey results and the user's research direction, evaluate candidate baselines:

1. **Review the survey's recommended baseline candidates**
2. **Evaluate each candidate** on:
   - Code quality and completeness (does the repo actually work?)
   - How representative it is of current SOTA
   - Compatibility with the user's research direction
   - Reproducibility (are the reported numbers achievable?)
   - Community adoption (stars, forks, citations)
3. **Select the primary baseline** — the one paper/codebase that this project will build on top of

## Output Format

Produce a baseline specification document:

```markdown
# Baseline Specification

## Task Setting
- Task: [e.g., "3D object detection from point clouds"]
- Setting: [e.g., "indoor scenes, ScanNet dataset"]

## Dataset(s)
- Primary: [name, version, split details]
- Auxiliary (if any): [name, purpose]

## Evaluation Metrics
- Primary metric: [e.g., "mAP@0.5"]
- Secondary metrics: [e.g., "mAP@0.25", inference time"]

## Primary Baseline
- Paper: [title]
- Venue: [venue + year]
- Code: [link]
- Expected performance: [report the original paper's numbers]
  - Metric 1: X.XX
  - Metric 2: X.XX
- Known reproduction issues: [any known problems]

## Why This Baseline
[2-3 sentences justifying why this is the right baseline to follow]

## Target Improvement
- Minimum viable improvement: [what would make this publishable]
- Stretch goal: [what would make this a strong paper]
```

## Confirmation

Present the baseline specification to the user and ask for explicit confirmation before proceeding. The user may want to:
- Change the dataset
- Choose a different baseline
- Adjust the metrics
- Modify the target improvement

Save the confirmed specification as `research-baseline.md`.
