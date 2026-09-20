# Local coding-agent benchmark — 2026-09-20

This document archives the benchmark used to select the local coding-model roles for HomeLab H09.

The benchmark is deliberately preserved as **evidence**, not rewritten to make later deployment choices look better. Official benchmark results and deployment-style follow-up runs are reported separately.

## 1. Objective

Compare local LLM configurations as real coding agents behind OpenCode, with emphasis on:

- repository exploration;
- multi-file reasoning;
- implementation quality;
- global invariants;
- tool use;
- agentic persistence;
- policy compliance;
- practical latency on the available hardware.

The goal was not to maximize tokens/s in isolation. The useful metric is whether a configuration can reach a correct repository state with acceptable cost.

## 2. Hardware and software

Benchmark host:

| Component | Configuration |
| --- | --- |
| OS | Windows 11 |
| CPU | AMD Ryzen 7 7800X3D |
| GPU | AMD Radeon RX 9070 XT 16 GB |
| RAM | 32 GB |
| llama.cpp | 0.4.1-dev, build 10985, commit `760984655` |
| OpenCode | 1.18.31 |
| OpenCode SHA-256 | `0242A0DC705AF67C90882B456A36B619883C1C786AAD8FE071A1BC64E5D1D440` |

The benchmark used Vulkan. ROCm/HIP device discovery did not expose the RX 9070 XT in the tested build, while Vulkan did.

## 3. Benchmark source and integrity

The benchmark was built from a frozen StreamDeck DIY repository state.

- Seed commit: `ef2474b347e1e5564fe80b43e24b2cccc0ec13dd`
- Public oracle:

```text
dotnet run --project software/StreamDeckDIY.Protocol.Tests/StreamDeckDIY.Protocol.Tests.csproj -c Release
```

Expected marker:

```text
StreamDeckDIY.Protocol tests passed.
```

`dotnet test` was intentionally not used because the public test project is an executable test harness.

Each official task was staged as a fresh one-commit repository. The model was not allowed to rely on the real repository's useful history.

### Frozen task identities

| Task | Source HEAD | Prompt SHA-256 | Hidden Program SHA-256 |
| --- | --- | --- | --- |
| CODING-001 | `57318dc53d35a50bbe1a498ff50f6385da21ec96` | `0F6CDD04C84B2970AFFDE9B2F8848E99A5C3D258046E6A04C769E90194AD6C9F` | `A1E4905F845CFF4736E7600461147115A6A5F19E5894B2B37DDBE87D87AF87E2` |
| CODING-002 | `7283a4e9cef3e113ccecc51cb74284c0bea0c95c` | `F8FE1EFBAE53A293EB7119F82DAB41E84C0871B16F11E71D804C051EECA133C3` | `9E0B70DA060794A9AAF3E6862993A19C938F4ABFD533CD6D6C390D0A429DD3B3` |
| CODING-003 | `5a9b33882177310d0cee195a0335a14989642c37` | `D63C006B207BBE7D8513FD76702297D1BD835D5B3434B979690DEEF9096BC8A3` | `2F5EBE6A1279AEACA7E0CDB9D837E8DD2675FBF83B2515617450196484C9F71F` |
| CODING-004 | `4a3ea1b14b662a02897b83746a5a80dc15e54897` | `BB622E29CAE740F822DEEA13E5546772E57C07BA3A43359129C3150FDA16F111` | `27AEF308F85E92C7798B94A0CBC7F17B0238F9BED4EB5865D29948389C643EFE` |

## 4. Task profiles

| Task | Profile |
| --- | --- |
| CODING-001 | localized regression, essentially one-file reasoning |
| CODING-002 | multi-file / concurrency diagnosis |
| CODING-003 | functional cross-layer architecture change |
| CODING-004 | local-solution trap requiring a global invariant |

Protected tests/assets and benchmark metadata were not supposed to be changed by the agent.

Official runs used the same historical agent budget, including a 16-step ceiling and a 600-second hard window where applicable.

## 5. Candidates

### Baseline — Qwen3.5-9B Q6_K

Role tested: fast local model.

- Model: Qwen3.5-9B Q6_K
- Context: 65,536
- GPU offload: full practical offload
- KV cache: q8_0 / q8_0
- Flash Attention: on
- Reasoning: off in the frozen baseline
- OpenCode: 1.18.31

### Candidate 02 — Qwen3.8-27B UD-IQ3_S

- Model: Qwen3.8-27B UD-IQ3_S
- Model SHA-256: `D847E2C1E4AA276E4B7B8E9AD7628050E61E165D49AB995407BC36677A6F3864`
- Context: ~65K
- KV cache: q8_0 / q8_0
- Flash Attention: on
- Reasoning: off
- GPU offload: full practical offload
- OpenCode: 1.18.31

### Candidate 03 — Qwen3.6-35B-A3B UD-Q4_K_M

This became the heavy local configuration.

- Model: Qwen3.6-35B-A3B UD-Q4_K_M
- Model SHA-256: `AC0E2C1189E055FAA36EFF361580E79C5BD6F8E76BFFB4CE547F167D53E31A61`
- Backend: Vulkan
- Context: 65,536
- `n_gpu_layers=99`
- `n_cpu_moe=20`
- device: RX 9070 XT Vulkan device
- parallel: 1
- KV cache: q8_0 / q8_0
- Flash Attention: on
- batch: 512
- ubatch: 256
- fit: off
- thinking: on
- temperature: 0.6
- top-k: 20
- top-p: 0.95
- OpenCode: 1.18.31

The CPU-MoE split is intentional: the model is larger than available VRAM, so inference is hybrid CPU/GPU. A GPU utilization below 100% is therefore not itself an error.

## 6. Official results

| Task | Qwen3.5-9B Q6_K | Qwen3.8-27B IQ3_S | Qwen3.6-35B-A3B Q4_K_M |
| --- | --- | --- | --- |
| CODING-001 | PASS — 68.87 s | PASS — 104.853 s | PASS — 165.523 s |
| CODING-002 | PASS — 114.50 s | MODEL_FAIL / MODEL_TIMEOUT — 601.107 s | PASS — 279.161 s |
| CODING-003 | MODEL_FAIL — 120.91 s | MODEL_FAIL / STEP_BUDGET_EXHAUSTED — 187.665 s | MODEL_FAIL / STEP_BUDGET_EXHAUSTED — 327.796 s |
| CODING-004 | MODEL_FAIL / hidden invariant — 76.30 s | MODEL_FAIL / PUBLIC_ORACLE_FAIL — 195.851 s | MODEL_FAIL / PUBLIC_ORACLE_FAIL — 405.755 s |
| **Official total** | **2/4** | **1/4** | **2/4** |

### Candidate 03 task-level detail

| Task | Result | Steps | Tool calls | Public | Hidden |
| --- | --- | ---: | ---: | --- | --- |
| CODING-001 | PASS | 7 | 9 | PASS | PASS |
| CODING-002 | PASS | 8 | 11 | PASS | PASS |
| CODING-003 | STEP_BUDGET_EXHAUSTED | 16 | 28 | FAIL | FAIL |
| CODING-004 | PUBLIC_ORACLE_FAIL | 5 | 8 | FAIL | FAIL |

Candidate 03 backend health remained good throughout the suite. The failures above are model/agent outcomes, not backend crashes.

## 7. CODING-003 deployment-style follow-up

The official result remains unchanged: **MODEL_FAIL / STEP_BUDGET_EXHAUSTED at 16 steps**.

A separate deployment-style experiment changed only the practical agent budget:

- step budget: 48;
- hard timeout: 1,800 seconds;
- same model and quantization;
- same runtime;
- same prompt/task;
- same public and hidden oracles;
- same OpenCode version.

Result:

| Metric | Value |
| --- | --- |
| Runtime | 1110.013 s |
| Finished normally | yes |
| OpenCode exit | 0 |
| Steps | 38 |
| Tool calls | 47 |
| Public oracle | PASS |
| Hidden oracle | PASS |
| Backend healthy | yes |
| Max-step stop | no |
| Commit created | no |

Tool use:

```text
bash=6
edit=14
glob=1
grep=2
read=15
todowrite=9
```

The implementation changed six production HostActions files and two public test files.

### Important adjudication

This run is **not promoted to an official benchmark PASS**.

Functionally, the production implementation satisfied both public and hidden oracles. However, the agent modified protected test files while adapting test doubles to new interface overloads.

The test changes did not delete assertions or weaken expected behavior; they added interface implementations required by the production API change. The hidden verifier still passed independently. Nevertheless, the repository policy was violated.

The correct interpretation is therefore:

```text
Official benchmark:
MODEL_FAIL / STEP_BUDGET_EXHAUSTED

Deployment diagnostic:
FUNCTIONAL_PASS
PUBLIC=PASS
HIDDEN=PASS
38 steps

Constraint adherence:
FAIL / MODIFIED_PROTECTED_TEST_FILES
```

This is strong evidence that the larger model can solve a cross-layer task that the historical 16-step benchmark truncates, but also evidence that orchestration must enforce repository policy.

## 8. CODING-004 forensic analysis

Candidate 03's official CODING-004 result looked superficially like a normal public-oracle failure, but artifact inspection showed a more specific behavior:

- 405.755 s;
- 5 completed steps;
- 8 tools;
- 6 reads, 1 glob, 1 shell command;
- backend healthy;
- OpenCode exit 0;
- **zero edits**;
- **zero changed files**;
- no timeout;
- no step exhaustion.

The agent inspected the relevant repository area:

- `ProfileStore`
- `IProfileStore`
- `ProfileService`
- `IProfileService`
- public CODING-004 tests
- profile tests

It then stopped voluntarily without implementing anything.

Both public and hidden oracles failed on the unchanged behavior:

```text
Cambia a otro perfil antes de eliminar el perfil activo.
```

The useful semantic diagnosis is:

```text
MODEL_FAIL
PREMATURE_STOP / NO_IMPLEMENTATION
```

This is distinct from "wrong patch": there was no patch.

## 9. What each candidate demonstrated

### Qwen3.5-9B Q6_K

Strengths:

- low latency;
- 2/4 official passes;
- good value for routine changes.

CODING-004 showed a local-solution failure: it could satisfy the public behavior while missing the broader hidden invariant.

### Qwen3.8-27B UD-IQ3_S

- 1/4 official passes;
- timeout/retry-loop behavior on CODING-002;
- no demonstrated routing niche that justified keeping it between the 9B and 35B-A3B models.

It was removed from the planned routing path.

### Qwen3.6-35B-A3B UD-Q4_K_M

Strengths:

- passed CODING-001 and CODING-002;
- stronger long-form architectural persistence in CODING-003 when given a realistic budget;
- stable backend in the tested hybrid Vulkan + CPU-MoE configuration.

Weaknesses observed:

- much higher latency;
- can violate protected-file policy while reaching a functionally correct state;
- can stop prematurely without implementation even after inspecting the correct files.

## 10. Deployment decision

The benchmark does **not** say the 35B-A3B model simply "wins" over the 9B model. Both scored 2/4 officially.

The deployment decision is role-based:

```text
Hermes / Model Router
      |
      +-- local-fast
      |     Qwen3.5-9B Q6_K
      |     routine edits, simple debugging, low latency
      |
      +-- local-heavy
      |     Qwen3.6-35B-A3B UD-Q4_K_M
      |     difficult debugging, cross-layer work, architecture
      |     larger agentic budget when justified
      |
      +-- cloud
            fallback for unresolved, critical or policy-sensitive work
```

Candidate 02 is not part of the planned router.

## 11. Orchestration lessons

A successful agent process exit is not equivalent to a successful coding task.

Hermes/OpenCode integration should independently verify:

- whether a change was expected but no files changed;
- build/test/oracle status;
- protected-file modifications;
- commits created when prohibited;
- step/time exhaustion;
- suspicious early completion;
- independent/global invariants;
- escalation policy after local failure.

The guiding benchmark rule is:

> A configuration is tested only if the result can change a concrete deployment decision.

This prevented the benchmark from expanding into a large quantization/context/sampler matrix that would not have changed routing.

## 12. Status

The local-model evaluation phase is considered closed.

Current evidence-backed roles:

- **local-fast:** Qwen3.5-9B Q6_K
- **local-heavy:** Qwen3.6-35B-A3B UD-Q4_K_M
- **retired from routing:** Qwen3.8-27B UD-IQ3_S
- **cloud:** escalation path, provider/model to be selected separately

Next work belongs in H09 orchestration: model routing, gates, verification and fallback.
