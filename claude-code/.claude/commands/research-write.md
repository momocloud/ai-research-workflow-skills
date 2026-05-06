Write a paper draft based on verified experiment results, ensuring every claim traces back to the research contract.

You are writing a research paper draft. Every claim must be backed by evidence from the research contract and experiment results.

## Input

Requires:
1. Research contract from `/research-contract`
2. Experiment results from `/research-experiment`
3. Analysis report from `/research-analyze`
4. Literature survey from `/research-survey`

If any are missing, ask the user to provide them.

## Non-Negotiable Rules

1. **Every claim must trace to a contract signal + experiment data.** If you cannot point to the specific experiment that supports a claim, do not make the claim.
2. **All numbers come from actual runs.** No fabrication, no estimation, no "approximately."
3. **The paper must be reproducible.** Anyone reading the method description must be able to re-implement and reproduce the results. Include all necessary details: hyperparameters, architectures, training procedures.
4. **Do not overclaim.** If the improvement is 2.3%, say 2.3%. Do not say "significant improvement" unless you have statistical significance tests.

## Paper Structure

### Abstract (150-250 words)
- Problem statement (1-2 sentences)
- Method summary (1-2 sentences)
- Key result with specific number (1 sentence)
- Significance (1 sentence)

### Introduction
- Context from the literature survey: what is the problem, why does it matter
- Current state of the art and its limitations
- This paper's contribution (numbered list, typically 2-4 items)
- Each contribution must be concrete and verifiable

### Related Work
- Organized by theme from the survey
- For each related work: what they did, how it relates, how this work differs
- Position this work clearly within the landscape

### Method
- Start from the research contract's method overview
- Add implementation details sufficient for reproduction
- Include: architecture details, loss functions, training procedure, key design choices
- Use figures to illustrate the architecture (describe what figures should show)

### Experiments
- **Setup**: dataset, metrics, baselines, implementation details (from contract)
- **Main results**: tables comparing with baselines (from experiment results)
- **Ablations**: each ablation from the contract, with results and analysis
- **Analysis**: insights from the analysis report

### Conclusion
- Summary of contributions
- Limitations (be honest — reviewers respect this)
- Future work (grounded in what the experiments revealed)

## Claim Tracing

For every factual claim in the paper, maintain a traceability matrix:

| Paper Section | Claim | Contract Signal | Experiment | Data Source |
|---------------|-------|-----------------|------------|-------------|
| Abstract | "improves mAP by 2.3%" | Signal 1 | Main run, seed 42 | `results/main/metrics.json` |
| Sec 4.2 | "attention improves over uniform" | Ablation 1 | Ablation run | `results/ablation_1/metrics.json` |

Include this matrix as an appendix. It ensures nothing is fabricated.

## Writing Style

- Be precise and quantitative. Prefer "improves by 2.3% (from 41.2% to 43.5%)" over "significantly improves"
- Be honest about limitations. If something only works in certain conditions, say so.
- Use active voice where possible
- Keep related work concise — this is not a survey paper
- Method section should be detailed enough for reproduction

## Output

Write the paper draft in LaTeX (preferred) or Markdown. Include:
- Complete text for all sections
- Table structures (with actual numbers from experiments)
- Descriptions of figures to be created
- The claim traceability matrix

Save as `paper-draft.tex` or `paper-draft.md`.

Remember: reproducibility is non-negotiable. A paper with fabricated or unreproducible results is worse than no paper at all.
