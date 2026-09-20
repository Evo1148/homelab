# Reproducibility and integrity notes

This benchmark archive is intentionally documentation-first. Large model binaries, API keys, runtime caches and machine-specific paths are not published.

## Frozen components

### OpenCode

- Version: `1.18.31`
- SHA-256: `0242A0DC705AF67C90882B456A36B619883C1C786AAD8FE071A1BC64E5D1D440`

Official invocation pattern:

```text
opencode run --format json --model <provider/model> --agent build --auto <prompt>
```

OpenCode stdout was captured as NDJSON and stderr separately.

### llama.cpp

- Version string: `0.4.1-dev`
- Build: `10985`
- Commit: `760984655`
- Compiler: Clang 20.1.8, Windows x86_64
- Backend selected for Candidate 03: Vulkan

### Model hashes

```text
Qwen3.8-27B UD-IQ3_S
D847E2C1E4AA276E4B7B8E9AD7628050E61E165D49AB995407BC36677A6F3864

Qwen3.6-35B-A3B UD-Q4_K_M
AC0E2C1189E055FAA36EFF361580E79C5BD6F8E76BFFB4CE547F167D53E31A61
```

The 9B baseline is retained as the established frozen baseline configuration from the benchmark session.

## Candidate 03 frozen runtime

Equivalent runtime intent:

```text
model: Qwen3.6-35B-A3B UD-Q4_K_M
context: 65536
n_gpu_layers: 99
n_cpu_moe: 20
device: primary discrete Vulkan GPU
split mode: none
parallel: 1
KV K: q8_0
KV V: q8_0
flash attention: on
batch: 512
ubatch: 256
fit: off
jinja: on
thinking: on
temperature: 0.6
top-k: 20
top-p: 0.95
```

The local HTTP endpoint and API-key file are deliberately omitted from this public repository.

## Agentic calibration before CODING suite

Before the official Candidate 03 suite, a disposable C# repository was used as an agentic smoke test.

Bug:

```text
1 << attempt
```

Expected fix:

```text
1 << (attempt - 1)
```

Observed Candidate 03 behavior:

- 124.567 s;
- 18 NDJSON events;
- 5 step starts / 5 step finishes;
- 6 tool calls;
- 1 production edit;
- exact correct one-line fix;
- tests passed;
- no commit;
- backend remained healthy.

This gate was calibration only and was not part of the four-task score.

## Official-task rules

- fresh one-commit opaque repository per task;
- no useful prior Git history for the model;
- no model-created commit;
- no web/subagents;
- public and hidden validation;
- protected test/assets/benchmark metadata not to be modified;
- official historical agent budget preserved for cross-candidate comparability.

The exact public/hidden behavior is task-specific. A public oracle passing at baseline does not by itself prove that the task is already solved; hidden/task-specific validation matters.

## Failure taxonomy used

```text
PASS

MODEL_FAIL
  ORACLE_FAIL
  PUBLIC_ORACLE_FAIL
  HIDDEN_INVARIANT_FAIL
  STEP_BUDGET_EXHAUSTED
  MODEL_TIMEOUT
  AGENT_ABORT
  POLICY_VIOLATION
  PREMATURE_STOP / NO_IMPLEMENTATION

INVALID_INFRASTRUCTURE
```

A timeout counts as a model failure only when the backend is healthy and valid model execution occurred.

## Candidate 03 CODING-003 deployment follow-up

This was deliberately separated from the official benchmark.

Only the practical agent budget changed:

```text
official step ceiling: 16
deployment step ceiling: 48
deployment hard timeout: 1800 s
```

No quantization, context, sampler, backend or model change was introduced.

The model used 38 steps and reached public + hidden PASS, but modified protected public test files. Therefore the run is archived as a functional success plus policy failure, not rewritten as an official benchmark pass.

## Candidate 03 CODING-004 forensic adjudication

No rerun was performed.

Existing artifacts showed:

- OpenCode exit 0;
- backend healthy;
- only 5 steps;
- 8 tools;
- relevant profile service/store code and tests inspected;
- zero edits;
- zero changed files;
- public and hidden failures on unchanged behavior.

Semantic diagnosis: `PREMATURE_STOP / NO_IMPLEMENTATION`.

## Sanitization

This public archive excludes:

- API keys and tokens;
- passwords;
- model GGUF binaries;
- personal filesystem paths;
- private LAN addresses;
- runtime databases/caches;
- `bin/` and `obj/`;
- giant raw logs that do not materially improve reproducibility.

Hashes, public model names, benchmark task identities and configuration parameters are intentionally retained.
