---
name: research-analyze
description: Analyze experiment results against the research contract with signal-by-signal assessment.
---

Analyze experiment results against the research contract with signal-by-signal assessment.

You are analyzing experiment results. This is where honest evaluation matters most. The research contract defines what success and failure look like — your job is to report objectively, not to find silver linings.

## Input

Requires:
1. Research contract from `/research-contract`
2. Experiment results from `/research-experiment`

If either is missing, ask the user to provide them.

## Process — Follow This Order Exactly

### Step 1: Read the Contract FIRST

Read `research-contract-v1.md` completely before looking at any results. You must internalize what was defined as success and failure before seeing what actually happened. This prevents unconscious bias.

### Step 2: Signal-by-Signal Assessment

For EVERY signal in the contract (both success and failure signals), evaluate against actual results:

| Signal | Type | Contract Criteria | Actual Result | Verdict |
|--------|------|-------------------|---------------|---------|
| [name] | Success | [e.g., "mAP ≥ X"] | [e.g., "mAP = Y"] | MET / NOT MET / INCONCLUSIVE |
| [name] | Failure | [e.g., "std > 1%"] | [e.g., "std = 0.3%"] | TRIGGERED / NOT TRIGGERED / INCONCLUSIVE |

Be precise. Use exact numbers. Do not round in your favor.

### Step 3: Ablation Analysis

For each ablation in the contract:

| Ablation | What It Tests | Expected | Actual | Match? |
|----------|---------------|----------|--------|--------|
| [name] | [description] | [expected outcome] | [actual outcome] | Yes/No/Partial |

If an ablation's actual result diverges significantly from the expected outcome, this is a valuable signal. Note it for follow-up.

### Step 4: Failure Analysis (If Applicable)

If results did not meet the success criteria:
- **Analyze WHY.** Do not rationalize. Do not find silver linings.
- Consider: Was the hypothesis wrong? Was the implementation correct? Were the hyperparameters suboptimal? Is the method fundamentally flawed?
- "Interesting negative results" are only valuable if you understand WHY they are negative.
- A small change that drops performance by 10 points tells you something important — the reverse direction might gain 10 points.

### Step 5: Follow-up Experiments

Based on the analysis, propose **specific, actionable** follow-up experiments:

1. **Diagnostic experiments**: Designed to answer "what caused the gap between expected and actual?"
2. **Analytical experiments**: Designed to understand which components contribute to performance
3. **Perturbation experiments**: What happens when we vary key hyperparameters?

For each proposed experiment, specify:
- What question it answers
- What data/setup is needed
- What outcome would confirm vs. refute the hypothesis
- Estimated compute cost

### Step 6: Idea Refinement

Based on ALL signals (successes, failures, ablation surprises):
- If the idea worked: what are the natural next steps to strengthen it?
- If the idea partially worked: what specific modifications could address the gap?
- If the idea failed: what did we learn? What does this failure suggest about alternative approaches?

Remember: ideas grow from experiments. The failures often teach more than the successes.

## Output Format

```markdown
# Analysis Report

## Contract Review Summary
- Total success signals: [N]
- Signals MET: [N]
- Signals NOT MET: [N]
- Signals INCONCLUSIVE: [N]
- Total failure signals: [N]
- Failure signals TRIGGERED: [N]

## Detailed Signal Assessment
[Table from Step 2]

## Ablation Analysis
[Table from Step 3]

## Failure Analysis (if applicable)
[Step 4 analysis]

## Follow-up Experiments
[Proposed experiments from Step 5]

## Idea Refinement
[Step 6 recommendations]

## Overall Assessment
[One paragraph: honest, direct summary of where things stand]
```

## Critical Rule

Do NOT say things like:
- "Although the main metric didn't improve, we observe interesting patterns..."
- "The method shows promise when viewed from another angle..."
- "This negative result is actually valuable because..."

UNLESS the contract explicitly defines those as success signals. Stick to the contract. If the contract says "mAP ≥ X" and you got mAP < X, that signal is NOT MET. Period.

This discipline is what separates honest research from storytelling.

Save the report as `research-analysis.md`.
