# Reference experiment: FFmpeg (C)

Goal: demonstrate that an agent can resolve a concrete runtime question without loading a code map. This recipe is proposed, not executed or validated here.

Hypothesis: software scaling activates libswscale code that does not execute during an otherwise comparable stream-copy operation. Encoding differences are a possible confound; this is a reachability comparison, not causal isolation of every differential region.

## Build

Use a pinned FFmpeg checkout in an isolated workspace on Linux or macOS, with Clang, matching LLVM tools, make and FFmpeg build prerequisites (including an assembler where required).

```sh
git clone https://github.com/FFmpeg/FFmpeg.git
cd FFmpeg
# Check out a chosen revision and record git rev-parse HEAD.
./configure --cc=clang --disable-shared --enable-static --disable-stripping \
  --extra-cflags="-fprofile-instr-generate -fcoverage-mapping -fprofile-update=atomic" \
  --extra-ldflags="-fprofile-instr-generate"
make -j4
mkdir -p runs
```

If toolchain flags or assembly dependencies fail, diagnose for the pinned revision rather than silently switching builds. FFmpeg's gcov toolchain is an alternative (`./configure --toolchain=gcov`, `make lcov`); isolate/reset counters for per-execution comparisons.

## Fixture and runs

```sh
LLVM_PROFILE_FILE=runs/fixture.profraw ./ffmpeg -y -f lavfi \
  -i testsrc2=size=640x480:rate=30 -t 1 -c:v mpeg4 runs/input.mp4

LLVM_PROFILE_FILE=runs/scale.profraw ./ffmpeg -y -i runs/input.mp4 \
  -vf scale=320:240 -c:v mpeg4 runs/scaled.mp4

LLVM_PROFILE_FILE=runs/copy.profraw ./ffmpeg -y -i runs/input.mp4 \
  -c copy runs/remuxed.mp4

for run in scale copy; do
  llvm-profdata merge -sparse "runs/$run.profraw" -o "runs/$run.profdata"
  llvm-cov export ./ffmpeg -instr-profile="runs/$run.profdata" > "runs/$run.json"
done
```

Run commands in a fresh scratch directory; the fixture profile is deliberately excluded from both comparisons. Record exit codes and verify output dimensions with ffprobe (use separately captured profiles if instrumented). If a run fails, label it incomplete rather than treating missing coverage as a zero hit.

Inspect the export for libswscale source regions with positive execution counts in scale versus copy. Compare exact locations from the same binary, not just file-level coverage percentages. Add an optional no-resize re-encode control to separate generic encode/decode from scale-specific activation. Repeat to check reproducibility.

## Expected report (not measured)

- Hypothesis: scale invokes software scaling implementation; stream copy does not.
- Evidence: actual positive hit locations and source references, output dimensions, exit status, comparison scope.
- Verdict: supported, contradicted or inconclusive based on measurements.
- Limitations: compiler coverage excludes handwritten assembly and uninstrumented dependencies, gives no precise call order, and may include unrelated startup and teardown work.
- Next experiment: no-resize re-encode or alternate pixel format, if needed.

Do not invent counts or claim a verdict before executing. FFmpeg FATE can seed future experiments, but a full regression-suite coverage run is not necessary for this question.

References: [FFmpeg development](https://ffmpeg.org/developer.html), [FATE](https://ffmpeg.org/fate.html), [LLVM coverage](https://clang.llvm.org/docs/SourceBasedCodeCoverage.html).
