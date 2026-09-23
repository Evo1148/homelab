# H09 Executor V0.1

H09 Executor V0.1 is the validated local coding-execution layer used by the HomeLab AI Agent Stack.

Validated on **2026-09-21**.

## Status

**READY**

The local pipeline has been validated end to end with real repository edits, independent verification, rollback, audit artifacts and automatic escalation from the fast local model to the heavy local model.

Cloud execution is intentionally **not implemented yet**.

## Runtime architecture

```text
User
  |
Hermes
  |
H09 coding skill
  |
H09 Executor
  |
  +-- deterministic router
  |
  +-- OpenCode persistent server
  |      |
  |      +-- local-fast
  |      |     Qwen3.5-9B Q6_K
  |      |
  |      +-- local-heavy
  |            Qwen3.6-35B-A3B UD-Q4_K_M
  |
  +-- repository policy
  +-- protected-file checks
  +-- verifier
  +-- audit trail
  +-- rollback
  +-- escalation
```

Current component versions:

| Component | Version / role |
| --- | --- |
| Hermes Agent | 0.21.3 |
| OpenCode | 1.18.31 |
| local-fast | Qwen3.5-9B Q6_K |
| local-heavy | Qwen3.6-35B-A3B UD-Q4_K_M |
| H09 Router | V0.1 |
| H09 Executor | V0.1 |

## OpenCode execution model

OpenCode runs as a persistent local server rather than starting a fresh embedded session for every task.

The repository-facing execution path uses the equivalent of:

```text
opencode run --attach <local-server> --dir <repository>
```

This replaced the earlier embedded execution path after repeated pre-inference session failures.

The persistent-server path was then validated with a real autonomous code edit.

## Routing

The router selects one of two local tiers:

### local-fast

Default path for routine and localized coding work.

Typical signals include:

- small fixes;
- one-file changes;
- localized edits;
- simple documentation changes.

### local-heavy

Used for harder work such as:

- architecture changes;
- cross-layer debugging;
- concurrency;
- global invariants;
- difficult multi-file refactors;
- root-cause analysis.

The heavy model is not used automatically for every task because the benchmark showed that the fast model remains much cheaper in latency while providing similar official benchmark coverage for routine work.

## Verification authority

A model process finishing successfully is not sufficient for H09 to report success.

The executor independently checks:

- whether an expected change actually happened;
- repository verifier exit status;
- protected-file changes;
- agent timeout;
- step-budget exhaustion;
- agent failure;
- suspicious no-change completion;
- unexpected commits;
- repository cleanliness and rollback state.

Representative classifications include:

```text
PASS
PREMATURE_STOP
VERIFY_FAIL
POLICY_VIOLATION
TIMEOUT
STEP_BUDGET_EXHAUSTED
AGENT_FAIL
```

Actions include:

```text
COMPLETE
ESCALATE_TO_HEAVY
ESCALATE_TO_CLOUD
BLOCK
```

## Automatic fast -> heavy escalation

The local escalation path is operational:

```text
local-fast
    |
    v
repository edit
    |
    v
verifier failure
    |
    v
ESCALATE_TO_HEAVY
    |
    v
rollback to clean baseline
    |
    v
local-heavy
    |
    v
repository edit
    |
    v
verifier pass
    |
    v
COMPLETE
```

The rollback happens before the heavy retry, so the second model receives the original clean problem rather than inheriting a failed first attempt.

## Validation evidence

### Gate 3D.2

A real local-fast execution:

- routed to Qwen3.5-9B;
- modified only the intended source file;
- passed the repository verifier;
- did not modify protected tests;
- did not create a commit;
- produced persistent OpenCode audit events;
- returned `PASS / COMPLETE`.

### Gate 3D.3

A real automatic escalation:

- local-fast performed a real edit;
- the first verifier result was forced to fail once;
- H09 classified it as `VERIFY_FAIL`;
- action became `ESCALATE_TO_HEAVY`;
- the repository was restored to the exact baseline;
- local-heavy executed from the clean baseline;
- the heavy attempt passed verification;
- protected files remained unchanged;
- no agent commit was created;
- both attempts produced independent audit artifacts;
- final result was `PASS / COMPLETE`.

## First real production task

After the validation gates were closed, H09 was exercised on a real issue in the public CAD-AI repository.

Task:

- `pyproject.toml` already declared version `0.2.0`;
- `src/cad_ai/__init__.py` still exposed `__version__ = "0.1.0"`;
- the repository policy protected tests, `pyproject.toml`, `.github/**` and the H09 policy itself.

Result:

- Hermes invoked the `h09-coding` skill;
- H09 routed the task to `local-fast`;
- OpenCode used Qwen3.5-9B Q6_K;
- exactly one source file was modified;
- the package version was aligned to `0.2.0`;
- independent verification passed;
- no protected files were changed;
- no commit was created by the agent;
- no heavy-model escalation was required;
- the orchestration completed in a single attempt with `PASS / COMPLETE`.

This was the first real production use of the complete path:

```text
User
  -> Hermes
  -> h09-coding
  -> H09
  -> OpenCode
  -> local-fast
  -> repository edit
  -> verifier
  -> PASS
```

## Hermes integration

Hermes exposes H09 through a dedicated local skill:

```text
h09-coding
```

The skill delegates repository modifications to the H09 pipeline instead of calling OpenCode directly.

The production boundary restricts H09-managed repositories to a dedicated workspace tree. Hermes receives only the privilege required to invoke the H09 wrapper, not unrestricted administrative access.

## Audit

Each model attempt stores its own execution artifacts, and multi-attempt orchestration stores a separate summary.

This makes it possible to distinguish:

- what the fast model did;
- why it was rejected;
- whether rollback succeeded;
- what the heavy model did;
- why the final result was accepted.

## Security

The public repository does not contain:

- API keys;
- passwords;
- local LLM secrets;
- private network addresses;
- model binaries;
- runtime databases;
- private repositories.

Published paths and examples are sanitized where appropriate.

## Next steps

The next useful work is operational rather than more local-model benchmarking:

1. Continue exercising Hermes -> H09 on real development tasks of increasing complexity.
2. Improve task/result presentation from Hermes.
3. Add cloud escalation only after the local workflow has enough real-world evidence.
4. Keep the verifier and repository policy authoritative over model output.

## Workflow hardening — 2026-09-23

Executor V0.1 remains the repository-facing coding engine, while the surrounding H09 platform now provides a deterministic task lifecycle.

Validated workflow:

`ProjectQueue → ExecutionBridge → ExecutionAdapter → H09AutoRunner → h09-code → h09-auto → ExecutionEvidence → VerificationBridge`

Additional validated properties include contract-hashed evidence, writer locking for mutating tasks, read-only no-lock semantics, fail-closed scope checks, persisted technical verification, Control Plane attempt fencing and lease renewal, physical local-model lifecycle switching and progress-aware execution.

The deterministic runner boundary and its real OpenCode + Qwen execution are now validated. The live HLC-001G task reached VERIFYING with persisted ExecutionEvidence, one authorized file changed, an unchanged Git HEAD, and no scope violation.
