# Runtime adapter strategy

Standardize observations rather than forcing every runtime into the same instrumentation technology. Prefer native tools and their existing formats. Select an adapter per target process, not just per repository extension.

| Target | Coverage | Supplemental runtime signals |
| --- | --- | --- |
| C/C++ | LLVM source-based coverage, GCC gcov/LCOV | perf or eBPF where supported; system call tracing |
| Rust | rustc LLVM instrumentation (-C instrument-coverage), compatible llvm-profdata/llvm-cov; cargo-llvm-cov for suitable workflows | tracing, perf |
| C#/.NET | dotnet-coverage (collect, server sessions where supported) | EventPipe / dotnet-trace |
| Java/JVM | JaCoCo javaagent (dump/reset or exit collection) | Java Flight Recorder |
| JavaScript/Node.js | V8 Inspector precise coverage; NODE_V8_COVERAGE for process-exit capture | Inspector, async context, diagnostics |
| TypeScript | V8 coverage plus matching source maps | Same as JS; validate source mapping |
| Python | coverage.py with branch mode and contexts where appropriate | sys.monitoring on supported Python versions, profiler or tracing hooks |

Browser JS may use Chrome DevTools Protocol precise coverage; workers and separate targets need explicit collection. Alternative engines require their own adapters.

## Adapter contract (proposed)

- detect: identify runtime/build/tool versions and report missing dependencies.
- prepare: configure an isolated instrumented build or authorized attach.
- observe: collect a bounded execution and capture native artifacts.
- normalize: emit observed locations/counts, capabilities, outcome and provenance.
- compare: compute like-for-like presence/count differences, report limitations.
- replay: reproduce a declared command and fixture if safe.
- cleanup: restore instrumentation settings and release resources.

An adapter declares capabilities (functions, regions, lines, branches, counts, call edges, timing, attach, reset, context attribution, subprocess coverage, source maps) and limitations. Runtime signal availability varies by tool, version, platform and configuration.

## Interoperability

Use existing native artifacts (LLVM JSON, LCOV, Cobertura, JaCoCo XML, V8 JSON, coverage.py data) rather than requiring all tools to emit one proprietary raw format. A small normalized JSON record carries provenance and references. Use W3C Trace Context / OpenTelemetry only when request or cross-service attribution is relevant. Distributed traces complement coverage, rather than providing line-level coverage.

For concurrency, use isolated instances and bounded windows initially. Process-wide counters cannot be attributed to individual overlapping requests merely by adding a trace ID. Track children, workers, native code and uninstrumented dependencies explicitly.

## References

- [LLVM source-based coverage](https://clang.llvm.org/docs/SourceBasedCodeCoverage.html)
- [GCC gcov](https://gcc.gnu.org/onlinedocs/gcc/Gcov.html)
- [Rust coverage](https://doc.rust-lang.org/rustc/instrument-coverage.html)
- [.NET coverage](https://learn.microsoft.com/en-us/dotnet/core/additional-tools/dotnet-coverage)
- [JaCoCo agent](https://www.jacoco.org/jacoco/trunk/doc/agent.html)
- [V8 precise coverage protocol](https://chromedevtools.github.io/devtools-protocol/tot/Profiler/)
- [Node CLI](https://nodejs.org/api/cli.html)
- [coverage.py contexts](https://coverage.readthedocs.io/en/latest/contexts.html)
- [Python monitoring](https://docs.python.org/3/library/sys.monitoring.html)
- [W3C Trace Context](https://www.w3.org/TR/trace-context/)
- [Agent Skills](https://agentskills.io/specification)

These are candidate adapters, not tested support claims.
