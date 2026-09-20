# H09 — AI Agent Stack

H09 is the HomeLab's 24/7 AI-agent layer.

The current architecture is intentionally split by role instead of trying to make one model handle every task:

```text
Hermes / Router
      |
      +-- local-fast
      |     Qwen3.5-9B Q6_K
      |
      +-- local-heavy
      |     Qwen3.6-35B-A3B UD-Q4_K_M
      |
      +-- cloud
            fallback / high-stakes tasks
```

OpenCode is the repository-facing coding agent. Hermes now delegates repository modifications through **H09 Executor V0.1**, which provides deterministic routing, verification, protected-file enforcement, persistent audit, rollback and automatic local-fast → local-heavy escalation.

## Why two local tiers?

A reproducible coding benchmark was run on 2026-09-20 against the StreamDeck DIY codebase.

The key result was not a single "winner":

- **Qwen3.5-9B Q6_K** remained the best fast local default: 2/4 official tasks, with much lower latency.
- **Qwen3.6-35B-A3B UD-Q4_K_M** also scored 2/4 officially, but a deployment-style rerun of the hardest architecture task showed that it could reach a functionally correct solution when allowed a larger agentic budget.
- **Qwen3.8-27B UD-IQ3_S** scored 1/4 and did not establish a useful routing niche between the two.
- The heavy model is therefore reserved for difficult multi-file debugging and architecture work rather than used for every edit.

The benchmark also showed why Hermes must verify agent outcomes instead of trusting a successful process exit:

- a model can finish with **no implementation** while returning exit code 0;
- a functionally correct implementation can still violate a repository policy such as modifying protected tests;
- public tests alone are insufficient for some global invariants.

## Verification gates

For repository tasks, the orchestration layer should check at least:

1. Did the task require changes, and were changes actually produced?
2. Did build/tests/oracles pass?
3. Were protected files modified?
4. Did the agent stop suspiciously early?
5. Did it hit a step/time budget?
6. Are hidden or independent invariants still satisfied?
7. If local execution fails, should the task be escalated to the heavy local model or cloud?

## Current executor status

H09 Executor V0.1 was validated end to end on 2026-09-21.

The production local path is:

```text
Hermes
  -> H09 coding skill
  -> deterministic router
  -> OpenCode persistent server
  -> local-fast
  -> verifier
```

When the first local attempt fails in an escalation-eligible way:

```text
local-fast
  -> failure
  -> rollback to clean baseline
  -> local-heavy
  -> verifier
```

This path has been exercised with real repository edits and independent audit artifacts. Cloud execution remains a future tier and is not yet implemented.

Full executor architecture and validation notes:

[H09 Executor V0.1](./executor-v0.1.md)

## Benchmark archive

Full methodology, frozen configurations, exact results and forensic notes:

[Local coding benchmark — 2026-09-20](./benchmarks/2026-09-20-local-coding/README.md)

Machine-readable results:

[results.csv](./benchmarks/2026-09-20-local-coding/results.csv)

Reproducibility and integrity notes:

[reproducibility.md](./benchmarks/2026-09-20-local-coding/reproducibility.md)

## Security

No API keys, passwords, model binaries, private environment files or live machine-specific credentials are stored here. Local paths and addresses are intentionally sanitized.
