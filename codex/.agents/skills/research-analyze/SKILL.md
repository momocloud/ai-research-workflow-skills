---
name: research-analyze
description: Trigger when user has experiment results and needs to analyze them against the research contract.
---

# Research Analysis

## Overview

Analyze experiment results against the research contract. This is a rigorous,
honest assessment — not a sales pitch. The contract defines success and failure.
The analysis checks each signal dispassionately.

## Prerequisites

- Research contract (approved version).
- Experiment results (from `research-experiment`).
- Baseline results (from `research-reproduce`).
- All three must be in clean context.

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

## Process

### Step 1: Read the Contract FIRST

Before looking at results, re-read the contract. Remind yourself of:
- The hypothesis.
- Success signals and their thresholds.
- Failure signals and their criteria.
- Ablation expectations.

This order matters. Reading results first creates anchoring bias. Reading the
contract first ensures you evaluate against what was promised, not what was
delivered.

### Step 2: Signal-by-Signal Assessment

Evaluate each signal defined in the contract:

```
### Signal Assessment

#### S1: [Success signal description]
- Contract threshold: [value]
- Achieved: [value]
- Verdict: **MET** / **NOT MET** / **INCONCLUSIVE**
- Evidence: [raw numbers, log references]
- Notes: [any caveats]

#### F1: [Failure signal description]
- Contract threshold: [value]
- Observed: [value]
- Verdict: **NOT TRIGGERED** / **TRIGGERED**
- Evidence: [raw numbers, log references]
- Notes: [any caveats]

[Continue for all signals]
```

**Verdict definitions:**
- **MET:** The threshold was reached or exceeded.
- **NOT MET:** The threshold was not reached, but the failure threshold was
  also not triggered.
- **INCONCLUSIVE:** Results are ambiguous (e.g., high variance across seeds,
  one seed met the threshold but others did not).
- **NOT TRIGGERED:** The failure condition did not occur.
- **TRIGGERED:** The failure condition did occur.

### Step 3: Ablation Analysis

Compare actual ablation results against contract expectations:

```
### Ablation Analysis

#### A1: [Ablation description]
- Expected: [what the contract predicted]
- Actual: [what happened]
- Alignment: **MATCHED** / **PARTIALLY MATCHED** / **MISMATCHED**
- Interpretation: [what this tells us about the component]

[Continue for all ablations]
```

Be specific about mismatches. If a component was expected to be critical but
removing it had no effect, that is an important finding. If a component was
expected to be optional but removing it crashed performance, that matters too.

### Step 4: Overall Assessment

```
### Overall Assessment

**Hypothesis:** [restate]
**Verdict:** [SUPPORTED / PARTIALLY SUPPORTED / NOT SUPPORTED / INCONCLUSIVE]

**Summary:**
- Success signals MET: [N] / [total]
- Failure signals TRIGGERED: [N] / [total]
- Ablations matching expectations: [N] / [total]
```

### Step 5: Failure Analysis

**This is the most important step.** If the hypothesis is NOT SUPPORTED or
PARTIALLY SUPPORTED, analyze WHY with intellectual honesty.

```
### Failure Analysis

**What went wrong:**
[Specific, mechanistic explanation. Not "it didn't work" but
"component X failed to produce useful representations because the
training signal was too weak, as evidenced by the loss plateauing
at epoch 20 while the baseline continued improving until epoch 50."]

**Why it went wrong:**
[Root cause analysis. Was the hypothesis flawed? Was the implementation
wrong? Was the hyperparameter choice bad? Was the task fundamentally
different from what the method was designed for?]

**What we learned:**
[Concrete findings from the experiment, even negative ones.
"The proposed attention mechanism does not help when the input
resolution is below 64x64, suggesting it requires fine-grained
spatial features to be effective."]
```

**Rules for failure analysis:**
- DO NOT find silver linings unless the contract defines them as success.
- DO NOT rationalize ("the improvement is small but statistically significant"
  when the contract said ">= 2.0 mAP" and you got 0.3).
- DO NOT blame the contract for being too strict. The contract was written before
  results and must be respected.
- DO analyze WHY. Understanding failure is more valuable than celebrating success.
- DO be specific. Reference specific numbers, logs, and ablations.

### Step 6: Follow-Up Proposals

Based on the analysis, propose concrete next steps:

```
### Follow-Up Proposals

#### Analytical Experiments
1. [Experiment to investigate a specific finding from the analysis]
   - Purpose: [what question it answers]
   - Expected cost: [GPU hours]

#### Idea Refinements
1. [Modified version of the current idea that addresses the failure mode]
   - What changes: [specific modification]
   - Why it might work: [mechanistic reasoning]
   - Risk: [what could still go wrong]

#### Alternative Directions
1. [Different approach based on what was learned]
   - Rationale: [why this is worth trying]
```

### Step 7: Present to User

Present the full analysis to the user. This is a **critical decision point**.
The user decides whether to:
- Iterate on the current idea (return to `research-idea` or `research-contract`).
- Run follow-up analytical experiments.
- Pivot to a different direction.
- Proceed to writing (if results are strong enough).

## Rules

- ALWAYS read the contract before looking at results.
- Evaluate each signal independently. Do not let one strong result bias the
  assessment of another.
- The contract's verdict is final. Do not invent new success criteria post-hoc.
- Failure analysis must be honest and mechanistic. No spin.
- "INCONCLUSIVE" is a valid verdict. High variance, seed sensitivity, and
  noisy metrics are real phenomena. Report them honestly.
- Never say "the results are promising" unless the contract defines the
  achieved result as a success signal.
