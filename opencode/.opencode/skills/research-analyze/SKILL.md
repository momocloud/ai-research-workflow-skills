---
name: research-analyze
description: Analyze experiment results against the research contract with signal-by-signal assessment.
---

# Research Analysis

## Trigger

Use this skill after `research-experiment` when experiments are complete and results need to be evaluated against the research contract.

## Prerequisites

- Approved `research-contract` (the original, unchanged version)
- Completed `research-experiment` results

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

### 1. Read the Contract FIRST

Before looking at any results, load the research contract. The contract defines the evaluation criteria. Analysis without the contract is just narrative, not evaluation.

**Do NOT look at results before reading the contract.** Reading results first creates confirmation bias -- you will unconsciously interpret results favorably.

### 2. Signal-by-Signal Assessment

Evaluate each signal defined in the contract independently.

#### Success Signals

| Signal | Metric | Contract Threshold | Actual Result | Verdict |
|--------|--------|--------------------|---------------|---------|
| [Primary] | [Metric] | [Threshold] | [Value] | MET / NOT MET / INCONCLUSIVE |
| [Secondary] | [Metric] | [Threshold] | [Value] | MET / NOT MET / INCONCLUSIVE |

For each signal:
- **MET**: The result meets or exceeds the threshold. Note the margin.
- **NOT MET**: The result falls short of the threshold. Note the gap.
- **INCONCLUSIVE**: The result is ambiguous (e.g., within noise margin, inconsistent across seeds). Explain why.

#### Failure Signals

| Signal | Contract Trigger | Observed | Triggered? |
|--------|-----------------|----------|------------|
| [Signal 1] | [Condition] | [Observation] | YES / NO |
| [Signal 2] | [Condition] | [Observation] | YES / NO |

Failure signals being triggered is serious. If a failure signal fires, the approach may be fundamentally flawed regardless of whether success signals are met.

### 3. Ablation Analysis

Compare each ablation result against:
- **The main experiment**: How much does removing this component hurt?
- **The contract's expected outcome**: Did the ablation behave as predicted?

| Ablation | Actual vs Main | Contract Expected | Match? |
|----------|---------------|-------------------|--------|
| [Name] | [Delta] | [Expected delta] | YES / NO / PARTIAL |

If an ablation contradicts the contract's expected outcome, this is a significant finding. It means the understanding of why the method works may be wrong.

### 4. Failure Analysis (If Applicable)

If success signals are not met or failure signals are triggered, perform honest failure analysis:

- **Analyze WHY, don't rationalize.** The goal is to understand the failure, not to find a way to call it a partial success.
- Consider: Was the hypothesis wrong? Was the implementation flawed? Were the hyperparameters suboptimal? Is the method fundamentally limited?
- Look at training curves, not just final numbers. Did the method plateau early? Did it overfit? Was it unstable?
- Compare against the baseline's behavior on the same metrics.

### 5. Follow-Up Proposals

Based on the analysis, propose concrete next steps:

#### If results are positive:
- What additional experiments would strengthen the finding?
- Are there edge cases or failure modes worth investigating?
- Would the method generalize to other datasets or tasks?

#### If results are negative:
- What specific analytical experiments could diagnose the failure mode?
- Are there targeted modifications (not "try everything different") that address the identified issue?
- Should the idea be revised (back to `research-idea`) or killed?

#### If results are inconclusive:
- What additional data or experiments would resolve the ambiguity?
- Is the inconclusive result due to noise, insufficient data, or a fundamental limitation?

### 6. Idea Refinement Suggestions

Based on the analysis, suggest specific refinements to the research idea:
- Which components of the method are most promising (from ablations)?
- What changes are most likely to improve results (with reasoning, not guessing)?
- Are there alternative formulations of the core idea worth exploring?

### 7. Honesty Rules

- **Do NOT find silver linings unless the contract defines them as success signals.**
- A result that misses the threshold by 0.1% is NOT MET, not "nearly met."
- A method that works on 2 of 3 datasets and fails on the third has a mixed result, not a "promising" one.
- Speculative positive interpretations belong in the "Follow-Up Proposals" section, not in the signal assessment.

### 8. Output

```markdown
## Analysis Report

### Overall Verdict: [SUCCESS / PARTIAL SUCCESS / FAILURE / INCONCLUSIVE]

### Signal Assessment
[Signal-by-signal tables as described above]

### Ablation Analysis
[Ablation comparison table]

### Failure Analysis (if applicable)
[Honest assessment of what went wrong and why]

### Key Findings
1. [Finding 1]
2. [Finding 2]

### Recommended Next Steps
1. [Specific, actionable recommendation]
2. [Specific, actionable recommendation]

### Decision Point
[Present to user: iterate (back to research-idea or research-contract) or proceed to research-write if results support it]
```

### 9. Handoff

Present the analysis to the user for a decision:
- If results support the hypothesis: proceed to `research-write`
- If results suggest refinements: iterate back to `research-idea` or `research-contract` (creating a v2)
- If results indicate fundamental failure: discuss whether to pivot or abandon
