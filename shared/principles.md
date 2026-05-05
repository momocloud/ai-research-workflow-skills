# AI Research Workflow — Core Principles

These principles govern every phase of the AI-assisted research workflow. They are extracted from real experience running deep learning experiments with Claude, GPT, and Gemini, and represent hard-won lessons rather than theoretical guidelines.

---

## 1. Context Is All You Need

Polluted context catastrophically degrades model ability. When the model keeps failing to fix a bug or keeps producing low-quality outputs, the problem is almost never capability — it is context. The context window has been corrupted by error traces, contradictory information, or accumulated noise from previous steps.

**Rule**: If a task is going poorly, do not try to patch within the existing session. Open a new session with clean, curated context. The cost of re-providing context is always lower than the cost of fighting corrupted context.

## 2. No Fabrication — Ever

All conclusions must have explicit provenance. Every idea proposed must map to a concrete, implementable method. Every experimental result must come from real code executing on real data. Every number in the paper must trace back to a specific log file or checkpoint.

**Rule**: If you cannot point to the exact code path and data that produced a result, it does not exist. "The model generated this number" is not a valid provenance.

## 3. Context Isolation

Each phase of the research workflow should operate in a clean, purpose-built context. Use sub-agents, background tasks, or new sessions to isolate:
- Installation and environment setup logs
- Download progress and error traces
- Intermediate failed attempts
- One task's context from another task's context

**Rule**: Never let verbose logs, error traces, or irrelevant discussion pollute the main conversation thread. Run dirty work in sub-agents; only bring back clean results.

## 4. The Research Contract Is Immutable Truth

Before any experiment begins, a Research Contract is written defining: hypothesis, success signals, failure signals, ablation plan, metrics, and hyperparameters. Once experiments start, the contract cannot be retroactively modified to fit results.

**Rule**: If the contract needs updating (e.g., a hyperparameter range was wrong), produce a v2 with explicit change log. Never silently adjust success criteria after seeing results. Post-hoc rationalization is the enemy of honest research.

## 5. Ideas Grow from Experiments, Not from Thin Air

Do not expect to propose a perfect idea upfront. The workflow is iterative: experiment, analyze results, learn from failures, refine the idea. Failures often provide stronger learning signals than successes ("if a small change drops performance by 10 points, the reverse might gain 10 points").

**Rule**: Design analytical experiments to understand why things work or don't work. Use these signals to iteratively refine ideas rather than trying to generate a flawless idea from scratch.

## 6. Human in the Loop

The AI proposes, the human disposes. Critical decision points that require human judgment:
- Environment setup and data paths (the AI will not read your docs reliably)
- Final idea selection (the AI can rank, but the human decides)
- Result interpretation (the AI will rationalize; the human must sanity-check)
- Any deviation from the research contract

**Rule**: When in doubt, pause and ask. A five-minute human check saves hours of AI going down a wrong path.

## 7. Multi-Model Ensemble When Available

Different models have different strengths:
- **Gemini**: Strongest search capability, broad knowledge, creative divergence
- **Claude**: Reliable architecture and code generation, structured thinking
- **GPT/Codex**: Strong reasoning for evaluation and analysis

Leverage each model's strength for the appropriate task. Cross-model review is a form of ensemble that is almost always beneficial.

**Rule**: Do not use a single model for everything. Match the task to the model's strength. When models disagree, that disagreement is itself a valuable signal.

## 8. Prefer LaTeX/Markdown Source Over PDF

When feeding papers into any model, prefer LaTeX source files or Markdown conversions over PDF. Models do not truly read PDFs end-to-end; they skim. With structured source, you can feed only the relevant sections (abstract + intro for survey, method for implementation), avoiding token waste and attention dilution.

**Rule**: arXiv papers almost always have LaTeX source available. Use it.

## 9. Run Dirty Work in Background, Never in Main Thread

Environment setup, package installation, dataset downloading, long training runs — these should never execute in the main conversation thread. They produce verbose logs that pollute context, and they block you from doing useful work in parallel.

**Rule**: Use sub-agents, background tasks, or tmux sessions for long-running operations. The main thread is for reasoning and decision-making, not for watching pip install scroll by.

## 10. Focus on Top-Venue Papers with Code

During literature survey, prioritize papers published at top venues (CVPR, ICCV, ECCV, NeurIPS, ICML, ICLR, ACL, EMNLP, etc.) that have open-source code. Venue quality is a proxy for rigor; available code is a prerequisite for reproducibility and for using as baselines.

**Rule**: A paper without code is significantly less useful as a baseline. A paper from an unknown venue requires extra scrutiny.
