---
name: entropy-skill
description: Reduce uncertainty about software behavior through controlled runtime experiments, native coverage and tracing, and concise evidence. Use when investigating reachability, debugging unexpected execution, comparing inputs, validating behavioral changes, or checking whether a code path actually runs.
---

# Entropy

Don't infer what you can observe.

## Workflow

1. Identify the decision or uncertainty. Read only the necessary source and existing tests. State a falsifiable hypothesis and what observation would distinguish it from an alternative.
2. Detect the target language, runtime, build/test commands and available instrumentation. Consult [adapters](references/adapters.md). Prefer existing project tooling and the least intrusive sensor that can answer the question.
3. Design the smallest controlled experiment: pin source/build identity, fixture, invocation, environment and relevant configuration. Compare two inputs or two revisions when useful; change one variable at a time. Isolate processes or serialize requests if attribution is process-wide.
4. Execute within an authorized local or test environment. Capture outcome, coverage or trace, and relevant side effects. Record failures and partial observations as such. Do not install tooling, modify application code, attach to production, send traffic externally or expose secrets without appropriate authorization. Set time/resource bounds; use synthetic inputs when possible.
5. Compare observed with expected behavior. Distinguish measured facts, interpretations and unknowns. Report the smallest useful evidence set, with source locations and artifact paths. Retain raw data locally for inspection; do not dump it into the agent context by default.
6. Recommend the cheapest next experiment that could resolve remaining decision-relevant uncertainty. Stop if the question is answered, additional experiments would not alter the decision, or the budget is exhausted.

## Signal selection

Choose only signals relevant to the hypothesis. See [signal contract](references/signals.md).

- Reachability / activation: did a code region run, and under which tested input?
- Differential execution: which regions occurred in A only, B only, or both?
- Behavioral drift: what outcomes or side effects changed between builds?
- Reproducibility: what varies across repeated trials?
- Coupling: observed co-activation or traced interactions (do not confuse them).
- Novelty / contradiction / blind spots: what new evidence matters, which assumption was contradicted, and what remains unobserved?

Prefer behavioral assertions plus execution evidence to coverage totals. Counts are not timing; coverage is not a call sequence. Absence of a hit only means not observed within the measured scope. Do not equate observed execution with correctness. Missing or unsupported signals are unknown, not zero.

## Report

Return a concise record:
- Question and hypothesis
- Experiment and comparison (commands, build/revision, fixture, scope)
- Verdict: supported, contradicted, inconclusive
- Evidence: relevant observations and source locations, with artifact references
- Limitations and remaining uncertainty
- Next experiment, only if needed

Do not claim tests were run if they were not. Do not claim a full application map. Use the tools available to you; proposed Entropy CLI commands are not yet implemented. See [FFmpeg reference experiment](examples/ffmpeg.md) for a starting pattern.
