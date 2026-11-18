# OpenOps Platform — Engineering Handbook

**Document status:** Maintained planning reference

**Reviewed:** 2026-11-17

## Purpose

OpenOps Platform is a collaborative engineering portfolio and learning project. Its initial workload is an intentionally small, independently runnable web application. The wider objective is to design, implement, secure, operate, and recover a portable, self-managed software-delivery platform around that workload.

The project prioritizes demonstrable engineering outcomes: small changes, reviewed code, reproducible tests, clear ownership, measurable acceptance criteria, and documented operational evidence. Complexity is introduced only when it serves a product requirement or a defined learning objective.

## Documentation map

| Area | Reference |
| --- | --- |
| Scope, non-goals, success criteria | [Project Charter](Project-Charter.md) |
| Team responsibilities and contribution process | [Engineering Governance](Engineering-Governance.md) |
| Anime catalog and follow-on products | [Application Product](Application-Product.md) and [Future Project Ideas](Future-Project-Ideas.md) |
| Browser language/runtime choices | [Frontend Architecture](Frontend-Architecture.md) |
| Components, contracts, and environment boundaries | [Platform Architecture](Platform-Architecture.md) and [Infrastructure and Environments](Infrastructure-and-Environments.md) |
| Source authority and Forgejo governance | [Forgejo and Repository Governance](Forgejo-and-Repository-Governance.md) |
| Runner isolation, CI, artifacts and updates | [CI and Supply Chain](CI-and-Supply-Chain.md) |
| Release promotion and Kubernetes reconciliation | [GitOps and Release Lifecycle](GitOps-and-Release-Lifecycle.md) |
| Team chat platform selection | [Chat Platform Evaluation](Chat-Platform-Evaluation.md) |
| Webhooks, bots and optional AI | [ChatOps and AI](ChatOps-and-AI.md) |
| Threat boundaries and security controls | [Security and Secrets](Security-and-Secrets.md) |
| Metrics, alerting and incident handling | [Observability and Reliability](Observability-and-Reliability.md) |
| Backups, restore and loss scenarios | [Backup and Recovery](Backup-and-Recovery.md) |
| Canonical forge transition and GitHub mirroring | [Migration and Mirroring](Migration-and-Mirroring.md) |
| Delivery stages and gates | [Roadmap and Acceptance](Roadmap-and-Acceptance.md) |
| Decisions awaiting validation | [Decision Register](Decision-Register.md) |
| Editing standards and update process | [Wiki Maintenance](Handbook-Maintenance.md) |

## Source-of-truth boundaries

- **Handbook:** research, alternatives, architectural context, hypotheses, and planning. A page labeled *Proposed* is not evidence of deployment.
- **Reviewed ADRs:** accepted interface or architecture decisions, promoted into the canonical code repository when they affect implementation.
- **Issues:** actionable work with an owner, testable acceptance criteria, and an outcome.
- **Pull requests:** implementation changes and review evidence.
- **Source repository:** tested code, manifests, operational runbooks, and reproducible commands.
- **Chat:** coordination and notifications; never the only durable record of a decision or incident.

## Current versus target state

At the 2026-11-17 baseline inspection, [`ctalaveraw/openops-platform`](https://github.com/ctalaveraw/openops-platform) contained only a minimal README on `main`. No application or platform deployment had been verified from this repository. This handbook is maintained separately in [`ctalaveraw/openops-platform-handbook`](https://github.com/ctalaveraw/openops-platform-handbook). Update these claims only when implementation evidence exists.

The proposed long-term direction is **Forgejo as canonical source/issue/PR system, with a separately owned handbook**, with GitHub as a public read-only Git mirror. That migration is **not recorded as complete**. CI, chat, AI, registry and GitOps selections are evaluated separately; they are not implied deployment commitments.

## Governing principles

1. A service must be able to operate independently of optional integrations.
2. One actor routinely owns each resource or category of state.
3. Build once; test and promote the same immutable artifact.
4. New infrastructure is provisioned declaratively and recovered from documented backups.
5. Untrusted code execution is isolated from source, secrets, and production credentials.
6. Engineering claims require reproducible evidence, not screenshots alone or successful command output without context.
7. All essential workflows must remain reproducible without proprietary hosted marketplace services.

See [Wiki Maintenance](Handbook-Maintenance.md) before changing published material.
