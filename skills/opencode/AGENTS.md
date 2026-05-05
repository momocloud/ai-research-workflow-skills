# AI Research Workflow — Cross-Cutting Principles

This document defines the principles that govern every skill in the AI Research Workflow. These are non-negotiable constraints designed to maximize research quality when using AI agents.

---

## 1. Context Is All You Need

Context pollution is the number one enemy of AI research quality. Every token in the conversation window either helps or hurts. There is no neutral.

- When stuck on a problem, the first action is to **reset the context** — start a fresh conversation rather than piling more instructions into a degraded one.
- Keep the main conversation thread surgical: only load the information needed for the current step.
- Summarize and close prior work before moving to the next stage. Do not carry raw logs, full paper texts, or debug output across stage boundaries.
- If the conversation feels like it is producing increasingly poor suggestions, it probably is. Reset.

---

## 2. No Fabrication

All results must have provenance. Every number in a paper, table, or report must trace back to real code execution.

- Never invent experimental results, even if they seem "reasonable."
- Never fabricate citation details, dataset statistics, or benchmark numbers.
- If a number cannot be produced from real runs, flag it explicitly as `UNVERIFIED` and resolve it before proceeding.
- When referencing paper results, cite the exact table/figure from the original.

---

## 3. Context Isolation

Use sub-agents for dirty work. Keep the main thread clean.

- Code execution, long training runs, environment setup, and log parsing happen in background processes or sub-agent calls.
- The main conversation thread should contain: task definitions, structured summaries, decision points, and clean results.
- Never paste raw multi-thousand-line logs into the main thread. Extract the signal, discard the noise.
- Use tmux sessions on remote servers so long-running processes survive disconnection.

---

## 4. Research Contract as Immutable Truth

Define success and failure criteria **before** running experiments. Never retroactively adjust them.

- The research contract (`research-contract`) is written before any experiment code is executed.
- Once experiments begin, the contract does not change. If the hypothesis or method evolves, create a new contract version (`v2`, `v3`) with an explicit changelog.
- Analysis (`research-analyze`) evaluates results **against the original contract**, not a rewritten one.
- This prevents the most common failure mode in AI-assisted research: unconsciously moving the goalposts.

---

## 5. Ideas Grow from Experiments

The workflow is not linear. It is iterative: experiment, analyze, refine.

- Research ideas should be grounded in concrete experimental observations, not abstract reasoning alone.
- When an experiment fails, the analysis should produce specific, testable follow-up hypotheses.
- The path from survey to paper is: survey, baseline, reproduce, idea, contract, experiment, analyze, iterate or write.
- Each cycle through the loop should sharpen the hypothesis and tighten the contract.

---

## 6. Human in the Loop

AI proposes. Human decides at critical points.

- Key decision gates: topic selection, baseline confirmation, idea selection, contract approval, experiment launch, paper submission.
- At each gate, present the AI recommendation with reasoning, then wait for explicit human approval.
- Never auto-advance past a gate. The human may redirect, revise, or halt at any point.
- When setup is problematic (environment issues, dependency conflicts), human-in-the-loop is the most reliable path.

---

## 7. Multi-Model Ensemble

Different models have different strengths. Use them intentionally.

- **Gemini**: Long context window, strong for literature search and synthesizing large document collections.
- **Claude**: Strong at careful code implementation, debugging, and structured reasoning. Preferred for implementation and analysis.
- **GPT**: Strong at evaluation, writing polish, and adversarial review. Good for "red team" analysis of ideas.
- Do not force a single model to do everything. Route tasks to the model best suited for them.

---

## 8. Prefer LaTeX Source over PDF

AI models do not truly read PDFs. They extract text poorly and lose structure.

- When possible, access papers via their LaTeX source (arXiv source files, conference repositories).
- Markdown or HTML versions are acceptable secondary options.
- Only fall back to PDF when no other format exists, and treat extracted text with appropriate skepticism.
- This applies especially during survey and baseline selection, where accurate understanding of method details matters.

---

## 9. Run Dirty Work in Background

Never block the main thread with long-running operations.

- `pip install`, dataset downloads, training runs, and evaluation sweeps run in background.
- Use `tmux` on remote servers. Use sub-agent invocations locally.
- Monitor progress with lightweight check-ins, not by streaming full logs.
- Keep the main thread available for reasoning and decision-making.

---

## 10. Focus on Top-Venue Papers with Code

Venue quality is a proxy for rigor. Code availability is a proxy for reproducibility.

- Prioritize papers from: CVPR, ICCV, ECCV, NeurIPS, ICML, ICLR, ACL, EMNLP, and equivalent top venues.
- Within venue-matched papers, strongly prefer those with publicly available code.
- This filters for both methodological quality and practical accessibility.
- When citing or building on work, verify the venue ranking rather than relying on citation count alone.

---

## Workflow Overview

The skills execute in this order, with iteration loops between experiment and analysis:

```
research-survey
    |
research-baseline
    |
research-reproduce
    |
research-idea
    |
research-contract
    |
research-experiment  <----+
    |                     |
research-analyze  --------+  (iterate if needed)
    |
research-write
```

Each skill receives only the outputs of prior skills (in clean, structured form), not raw conversation history. This context discipline is what makes the workflow reliable.
