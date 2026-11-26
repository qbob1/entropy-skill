# Entropy

**Empirical runtime intelligence for software agents.**

> Don't infer what you can observe.

Entropy is an agent skill for reducing uncertainty about software through controlled runtime experiments. It uses existing instrumentation rather than inventing a new profiler, dashboard or code map.

## Why

Agents have excellent access to static code, but often make unsupported assumptions about which paths run, what triggers them, what changes after a patch and which side effects occur. More code context is not necessarily more information. Entropy collects narrowly scoped runtime evidence to inform a decision.

## Use the skill

Clone this repository and make its root skill directory (containing [SKILL.md](SKILL.md)) available to an Agent Skills-compatible client, under the name `entropy-skill`. Installation locations vary by client. Ask the agent to investigate a specific runtime question, such as:

- Does this handler actually execute when the request fails?
- Which functions are exclusive to input A versus input B?
- Does this patch change the expected outcome or execute unexpected code?
- What is the smallest reproducible experiment that resolves this uncertainty?

The skill can use native tools directly; no Entropy executable is required. Proposed CLI commands in the design are future interfaces, not implemented commands.

## Design

- [SKILL.md](SKILL.md): agent workflow, evidence rules, and output contract.
- [Runtime adapters](references/adapters.md): C/C++, Rust, C#, Java, JS/TS, Python.
- [Signal contract](references/signals.md): what to collect, compare, and report.
- [FFmpeg experiment](examples/ffmpeg.md): first reference target.
- [Roadmap](ROADMAP.md): incremental implementation and evaluation.

## Non-goals

No full code-map UI, database, always-on telemetry server, mandatory MCP service, coverage percentage target, or autonomous code changes. Raw coverage is an artifact, not the answer. Coverage is evidence of execution, not proof of correctness or non-reachability.

## Status

Initial skill specification and proposed reference recipes. The adapters and CLI are not packaged or validated yet; no FFmpeg experiment results have been measured in this repository. Start with the native tools and verify their output on the target build.

