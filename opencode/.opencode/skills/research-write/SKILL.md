---
name: research-write
description: Write a paper draft based on verified experiment results, ensuring every claim traces back to the research contract.
---

# Research Paper Writing

## Trigger

Use this skill after `research-analyze` when experiment results have been validated against the research contract and the user is ready to write a paper draft.

## Prerequisites

- Approved `research-contract`
- Completed `research-experiment` results
- Completed `research-analyze` report with clear signal assessments

## Contract Integrity Check

Before proceeding, verify the research contract has not been modified since experiments began:

1. Check if `.research/v1.sha256` exists:
   - **If NOT**: the contract was never locked. Warn the user — experiments may not have been run through `research-experiment`. Suggest running it first.
   - **If YES**: verify integrity:
     - Current hash: `sha256sum research-contract-v1.md | awk '{print $1}'`
     - Stored hash: `cat .research/v1.sha256`
     - **Match**: contract is intact. Proceed.
     - **Mismatch**: STOP. The contract was modified after experiments began. Show the diff:
       `diff research-contract-v1.md .research/contracts/v1.lock.md`
       The user must resolve this before proceeding — either revert the contract or create a v2 with changelog.

Note: on macOS, use `shasum -a 256` instead of `sha256sum`.

## Instructions

### 1. Traceability Is Non-Negotiable

Every claim in the paper must trace back to:
- A specific signal in the research contract
- Data from real experiment runs
- A specific table, figure, or measurement

If a result cannot be reproduced from the experiment logs, it does not go in the paper. No exceptions.

### 2. Structure

Follow standard academic paper structure:

#### Abstract (250 words max)
- Problem statement (1-2 sentences)
- Gap in existing work (1 sentence)
- Proposed method (1-2 sentences)
- Key result with specific numbers (1-2 sentences)
- Broader impact (1 sentence, optional)

#### Introduction (1 page)
- Problem context and motivation
- Limitations of existing approaches
- Summary of the proposed method
- Key contributions (bullet points, each with specific, verifiable claims)
- Paper organization

#### Related Work (1-1.5 pages)
- Organized thematically, not chronologically
- For each related work: what they do, how it differs from this work
- Ground in the survey from `research-survey`
- Position this work clearly relative to existing approaches

#### Method (2-3 pages)
- Problem formulation
- Method description with clear mathematical notation
- Implementation details matching the contract's hyperparameters
- Any deviations from the contract noted and justified

#### Experiments (2-3 pages)
- Experimental setup (matching contract: datasets, metrics, hardware)
- Main results table with contract signal assessments
- Comparison with baseline (using reproduced numbers, not paper-reported numbers)
- Ablation study matching the contract's ablation plan
- All numbers from real runs, clearly labeled

#### Analysis (1-2 pages)
- Signal-by-signal assessment from the analysis report
- When things worked: why (supported by ablations)
- When things did not work: honest discussion
- Limitations of the current approach

#### Conclusion (0.5 page)
- Summary of findings
- Key takeaways
- Future directions grounded in analysis findings

### 3. Writing Principles

- **Specific over vague**: "improves mAP by 2.3 points" not "achieves significant improvement"
- **Honest over impressive**: report actual numbers, not cherry-picked best seeds
- **Concise over verbose**: every sentence should convey information
- **Reproducible over clever**: include all details needed for reproduction
- **Contract-aligned**: every experimental claim maps to a contract signal

### 4. Figures and Tables

Produce or specify the following:

**Tables:**
- Main results table: method vs baseline vs any other comparisons, all contract metrics
- Ablation table: each component's contribution
- Any additional analysis tables from the analysis report

**Figures:**
- Method overview diagram (architecture or pipeline)
- Training curves (loss and primary metric)
- Qualitative examples (if applicable)

All tables and figures must use data from actual experiment runs. No placeholder data.

### 5. Numbers Discipline

Every number in the paper must satisfy this checklist:
- Comes from a real experiment run (not estimated or guessed)
- Can be traced to a specific log file or results file
- Is reported with appropriate precision (do not over-specify)
- Includes variance if multiple seeds were run (per contract)
- Is compared against the contract threshold for context

### 6. Self-Review Checklist

Before presenting the draft to the user, verify:

- Every claim in the contributions section is supported by experiment results
- All numbers in the experiments section come from real runs
- The method description matches what was actually implemented (not what was planned)
- Ablation results are consistent with the method description
- Limitations section honestly addresses weaknesses
- Related work accurately represents existing methods
- The paper could be used to reproduce the experiments

### 7. Output

Produce a complete paper draft in Markdown (or LaTeX if the user prefers) with:
- All sections filled in with real content
- Tables with actual numbers
- Placeholders only for figures that require manual creation (e.g., diagrams)
- A notes section listing any remaining TODOs (e.g., "need to generate qualitative examples")

### 8. Handoff

Present the draft to the user for review. Flag:
- Any claims that could not be fully supported by data
- Any sections that need human refinement (e.g., positioning, framing)
- Any figures that need to be manually created
- Suggested target venue and any venue-specific formatting needs
