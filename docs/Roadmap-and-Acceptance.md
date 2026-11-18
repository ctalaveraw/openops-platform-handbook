# Roadmap and Acceptance Gates

**Status:** Proposed; sequencing may change based on experimental results

**Reviewed:** 2026-11-18

Milestones represent demonstrable capabilities, not timeboxed commitments. A gate closes only when another engineer can reproduce the stated result. The project can progress with one contributor; work should not depend on Ben, Alex or a particular homelab remaining available.

| Gate | Deliverable | Evidence required |
| --- | --- | --- |
| M0 — Working agreement | Charter, role model, source/workflow conventions and initial ADRs | Contributor creates PR and runs local validation |
| M1 — Product MVP | Minimal static anime catalog and watchlist | Browser acceptance tests; schema fixtures; local reload behavior |
| M2 — Container | Small, non-root static HTTP image | Runtime HTTP check, image-size measurement, no cluster needed |
| M3 — Portable CI and first ChatOps | GitHub Actions using portable tools; native GitHub-to-Zulip notifications | Local vs CI tests match; PR/review hook, CI failure and chat-outage tests |
| M4 — CI qualification | Portable validators, trusted publication, immutable artifact | Synthetic failure, untrusted PR isolation and exact digest retrieval |
| M5 — Zulip acceptance | Self-hosted Zulip with scoped GitHub integration, bots and backups | PR/review, bot permission, notification outage, search and restore evidence |
| M6 — GitOps release | One reconciler + reviewed digest promotion | Drift correction, bad-image rollout diagnosis, Git revert recovery |
| M7 — Platform security | Scoped authority, scans, artifact validation, secrets | Positive/negative enforcement tests, exceptions, rotation |
| M8 — Observability and recovery | End-to-end monitoring, failure drills, off-host restore | Trace from source revision to running digest, incident and recovery |
| M9 — Second environment / optional Forgejo rehearsal | Cloud/other Kubernetes target, and disposable Forgejo Actions portability test | Same image digest, isolated environment config, plus required [capability tests](Capability-Parity-and-DX.md) across CI providers |
| M10 — Second product | Linux server automation pilot, separately scoped | Read-only discovery and permission/failure testing before mutation |

## Prioritization

Deliver M0–M2 before standing up the complete management plane. GitHub stays authoritative during M3–M5. A disposable Forgejo rehearsal can run independently of any decision to migrate. [DX issue #7](https://github.com/ctalaveraw/openops-platform/issues/7) tracks the provider-neutral capability contract and testing. Do not claim 'full GitOps' because a manifest exists or 'DevSecOps' because one scanner is green. Record each boundary actually exercised.

## Issue slicing

Keep issues independently testable. Examples: validate anime JSON in Python; persist watchlist state; compare static HTTP servers; write one Playwright smoke test; publish one immutable image; reject one untrusted PR secret; observe one Flux drift correction. Each issue links to one source change and one test/evidence outcome.

## Evidence format

Each gate should have a short report identifying revision, environment, commands or test identifiers, expected/observed outcomes, unexpected findings, rollback behavior and follow-up work. Raw logs may be stored as artifacts; the report must remain readable without them.

## Change control

Update this roadmap as a coherent whole when evidence changes priorities; do not accumulate contradictory 'planned/done' lines. Transition a proposed architecture decision to an accepted ADR before hard-coding it as an irreversible dependency.
