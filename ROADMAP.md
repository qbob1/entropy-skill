# Roadmap

## V0 — skill-first

- Agent Skills-compatible instructions and reference recipes.
- FFmpeg controlled differential experiment using native LLVM tools.
- Record reproducible measurements and evidence-only summary.
- Validate one question end-to-end, including a negative or inconclusive case.

## V1 — portable adapters

- Shared LLVM normalization for C/C++ and Rust; build-specific preparation.
- Node JS/TS V8 adapter (source maps, then isolated long-running sessions).
- Python coverage adapter (contexts, branch coverage).
- C#/.NET and Java/JVM adapters.
- Capability negotiation and explicit incomplete/unsupported signals.

## V2 — efficient experiments

- Compare and replay records; stable same-build identities and cautious cross-build mapping.
- Repeated-run stability; input corpus only when it improves reproduction.
- Optional supplemental traces, side effects and distributed request attribution.
- Benchmark agent decisions against static-only reasoning: correct conclusions, unsupported claims, cost, time, context volume and reproducibility.

## Candidate CLI (not implemented)

```sh
entropy detect
entropy observe -- <command>
entropy compare run-a run-b
entropy inspect run-a --symbol <name>
entropy replay run-a
```

A probe is a bounded experiment designed to distinguish hypotheses. The skill can orchestrate native commands without a custom CLI.

## Acceptance criteria

An agent can answer whether a targeted path ran, reproduce the observation, compare two executions, and identify limitations across each target language. It should cite evidence without consuming the entire raw coverage artifact. Unsupported observations must be explicit. The same report should be understandable by a human and another agent.

Defer dashboards, MCP servers, databases, broad static maps and autonomous fuzzing unless a demonstrated decision-making need justifies them.
