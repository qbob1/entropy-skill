# Signal contract

## Objective

Maximize decision-relevant information gain, not coverage percentage or context volume. A single branch that falsifies an assumption may be worth more than thousands of newly observed lines.

| Signal | Evidence | Interpretation boundary |
| --- | --- | --- |
| Reachability | Positive hit at a source location | Non-hit is not proof of unreachable code |
| Activation | Input, configuration, scoped hit | An observed trigger is not necessarily the only trigger |
| Differential execution | A-only, B-only and shared regions | Compare equal instrumentation and mapped identities |
| Behavioral drift | Outcome/assertion/side-effect changes | Pin environment; isolate changes from noise |
| Reproducibility | Repeated outcomes and signal variance | Record trial count and seeds |
| Coupling | Co-activation or explicit call/trace edges | Co-activation alone does not establish causality |
| Side effects | Observed file, network, database or child-process events | Record sensor scope; avoid secrets |
| Novelty | Previously unseen relevant observation | Relative to a defined corpus, not universal |
| Contradiction | Evidence incompatible with a stated prediction | Declare alternative explanations |
| Blind spots | Relevant missing instrumentation or untested conditions | Unknown, not zero |

## Observation record (illustrative, v0.1 proposal)

```json
{
  "schema": "entropy/observation/v0.1",
  "run_id": "example-a",
  "hypothesis": "Scaling activates libswscale",
  "revision": "example-revision",
  "build_id": "example-build",
  "adapter": "llvm",
  "tool_versions": {},
  "invocation": {"argv": ["ffmpeg", "..."], "fixture_id": "video-001"},
  "scope": {"process": "isolated", "granularity": ["region", "function"]},
  "outcome": {"exit_code": 0, "assertions": []},
  "observations": [],
  "artifacts": ["runs/example-a/coverage.json"],
  "limitations": [],
  "verdict": "inconclusive"
}
```

A location uses a repo-relative path, source span, optional symbol and build/revision-specific identity. A count, if available, has explicit units and sensor scope. Store native artifact references and source mapping information rather than assuming line numbers are stable across builds. Mark each signal observed, unsupported, partial or not collected. Do not treat unsupported values as zero.

For comparisons, identify both runs and common scope, then report A-only/B-only/shared regions and changed outcomes. Across revisions, use source diff/symbol mapping and mark ambiguous matches; never compare raw IDs across builds. For noisy runs, repeat and report variance. Only report meaningful deltas by default.

## Storage

Local JSON/JSONL plus native artifacts and optional replay fixtures are sufficient. Keep generated profiles and potentially sensitive captures outside version control. Store only redacted, synthetic, or approved fixture material. Prefer content hashes for large fixtures; pin tool versions, revision, flags and environment metadata (without secrets).

## Information gain

Conceptually, information gain for an experiment e is prior hypothesis entropy minus expected posterior entropy after observing e. No numerical entropy estimator is required in v0: state competing hypotheses, expected discriminating outcomes, observations and remaining unknowns. A successful experiment changes or justifies a decision.
