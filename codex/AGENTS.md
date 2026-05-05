# AI Research Workflow — Cross-Cutting Principles

This document defines the principles that govern every skill in the AI Research Workflow.
All agents operating within this workflow MUST follow these principles without exception.

---

## 1. Context Is All You Need

Context pollution is the **#1 enemy** of AI research quality. A model working with
clean, relevant context will consistently outperform a model drowning in stale logs,
error traces, and tangential information.

- **When stuck, reset context.** Do not soldier on with degraded context.
  Open a fresh session or sub-agent rather than accumulating noise.
- **Never let logs, stack traces, or verbose output linger in the main thread.**
  Pipe them to files. Summarize results. Move on.
- **Treat context window as a scarce resource.** Every token has a cost in
  attention and reasoning quality. Be ruthless about what stays.

---

## 2. No Fabrication

Every result must have **provenance**. Every number must come from real code execution.

- Never invent experimental results, metrics, or citations.
- Never paraphrase a paper's findings without having read the source material.
- If you do not know something, say so explicitly.
- All performance numbers must be traceable to a specific run, log file, or
  published table in a paper.
- When referencing a paper, verify the claim against the actual text before citing it.

---

## 3. Context Isolation

Use **sub-agents** for dirty work. Keep the main conversation thread clean.

- Setup scripts, long training runs, dependency resolution, and data downloading
  all belong in sub-agents or background processes.
- The main thread should only see: the task, the plan, and the summarized result.
- When a sub-agent fails, read only the **summary of the failure** into the main
  thread — never the full traceback.
- Structure work so that each sub-agent has a single, well-scoped responsibility.

---

## 4. Research Contract as Immutable Truth

The research contract is the **single source of truth** for an experiment.

- Define success criteria, failure criteria, metrics, thresholds, and ablation
  plans **before** running any experiments.
- Once experiments begin, the contract is **frozen**. Any changes require a formal
  version bump (v1 -> v2) with a changelog explaining what changed and why.
- Never retroactively adjust the contract to match results. If the results don't
  meet the contract, the honest answer is "failure signal met" — then analyze why.
- The contract protects against post-hoc rationalization and moving goalposts.

---

## 5. Ideas Grow from Experiments

Research is iterative. The cycle is:

```
experiment -> analyze -> refine idea -> new experiment
```

- Do not over-plan. Run a small experiment early to validate assumptions.
- Let results inform the next idea, not the other way around.
- Negative results are valuable data. Document them. Learn from them.
- Each iteration should produce a clear artifact: a metric, a plot, or a
  finding that feeds into the next cycle.

---

## 6. Human in the Loop

AI proposes. Human decides. This is the boundary.

- **Critical decision points** require explicit human approval:
  - Selecting the baseline paper
  - Choosing the research idea to pursue
  - Signing off on the research contract
  - Deciding to write up results
  - Any deviation from the contract
- Never autonomously advance past a critical decision point.
- Present options with clear trade-offs. Make a recommendation. Wait for approval.

---

## 7. Multi-Model Ensemble

Different models have different strengths. Use them deliberately.

| Model  | Strength                         | Use For                              |
|--------|----------------------------------|--------------------------------------|
| Gemini | Search, large context, web       | Literature search, paper discovery   |
| Claude | Code generation, careful reason  | Implementation, analysis, writing    |
| GPT    | Evaluation, structured output    | Independent review, evaluation       |

- Use the right model for the right task. Do not force a single model to do everything.
- When possible, get an independent evaluation from a different model than the
  one that generated the work.

---

## 8. Prefer LaTeX Source Over PDF

Models do not truly read PDFs. Text extraction from PDF is lossy and error-prone.

- Always prefer LaTeX source, Markdown, or HTML over PDF when available.
- ArXiv papers often have LaTeX source. Use it.
- Conference websites sometimes provide HTML versions. Prefer those.
- If only PDF is available, extract text carefully and note that extraction
  may have introduced errors.
- Never trust a formula extracted from PDF without verification.

---

## 9. Run Dirty Work in Background

Long-running, noisy, or failure-prone operations must never block the main thread.

- **pip install**, **conda install**, **apt-get** — always in background or sub-agent.
- **Training runs** — always in background. Use `tmux` on remote servers.
- **Data downloads** — always in background. Verify checksums after completion.
- **Setup scripts** — always in background. Summarize success/failure only.
- The main thread should remain responsive and focused on reasoning.

---

## 10. Focus on Top-Venue Papers with Code

Venue quality is a proxy for rigor. Code availability is a proxy for reproducibility.

- Prioritize papers from top venues:
  - **Vision:** CVPR, ICCV, ECCV
  - **ML:** NeurIPS, ICML, ICLR
  - **NLP:** ACL, EMNLP
  - **Systems:** OSDI, SOSP, MLSys
- Among equally relevant papers, prefer the one with open-source code.
- A paper from a top venue with code and 50 citations beats a workshop paper
  with 500 citations and no code.
- When a non-top-venue paper is genuinely relevant, note the venue and include it,
  but do not treat it as equal authority.

---

## Workflow Ordering

The skills are designed to be invoked in sequence:

```
research-survey -> research-baseline -> research-reproduce -> research-idea
-> research-contract -> research-experiment -> research-analyze -> research-write
```

However, iteration is expected. After `research-analyze`, it is normal to return
to `research-idea` or `research-experiment` for another cycle. After `research-write`,
reviewers may require new experiments.

The contract is the anchor point. Everything before it is exploration.
Everything after it is execution. The contract is the boundary between
"what might work" and "we are testing this specific hypothesis."
