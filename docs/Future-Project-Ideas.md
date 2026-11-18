# Future Product and Platform Experiments

**Status:** Idea register; no implementation commitment

**Reviewed:** 2026-11-17

The first workload is a small browser application. Later products should reuse the same platform interfaces and deliberately introduce one new class of operational problem at a time.

## Anime application expansions

- Public metadata API integration, with explicit licensing, rate-limit and browser CORS evaluation.
- Local watchlist import/export and schema evolution.
- Optional synchronization between devices through a separate API and storage service.
- Recommendation functionality backed by documented data and testing, without mandatory LLM dependence.
- Browser telemetry, accessibility and visual regression testing.

These features become independent issues only after the static MVP exists and a concrete need is demonstrated.

## Linux server automation platform

A second candidate product manages Linux hosts, patching, service health, configuration drift and inventory. It is operationally sensitive and should not be the initial project. A staged design could begin with read-only host inspection and a structured JSON output contract, then add agent enrollment, scheduling, authenticated change operations and audit records after security gates are proven.

Two possible data products: a human-readable log stream for operators and structured JSON/JSONL for aggregation by other tooling. Both outputs should be generated from the same underlying typed event model. Ansible-host baselining may provide enrollment, but Ansible inventory must not implicitly grant permanent application-level remote execution authority.

## Reusable platform capabilities

- A standardized application onboarding contract that accepts static images or simple HTTP services.
- A portable CLI test/publish command with consistent exit codes and evidence paths.
- A self-hosted dependency/action mirror and trusted update process.
- A scoped read-only ChatOps bot for build, release and cluster health queries.
- A low-complexity internal service catalog only when several workloads justify it.
- A second cloud deployment target with IAM/OIDC and cost guardrails.
- Failure-injection and disaster-recovery training exercises for other engineers.

## Experiment intake

Every proposed idea should answer: who benefits; what new capability is learned; the smallest usable result; new state and privilege required; how it is tested; how it fails; and what can be removed if abandoned. Avoid a catalog of speculative tools with no measurable outcomes.
