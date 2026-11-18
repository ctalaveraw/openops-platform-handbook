# Work Tracking and Dependencies

**Status:** Planned, issue inventory created; implementation state remains independently verified per PR  
**Baseline:** 2026-11-18  
**Canonical work tracker:** [OpenOps issues](https://github.com/ctalaveraw/openops-platform/issues)  
**Policy:** GitHub remains authoritative for source and CI today; Zulip is the selected collaboration direction; Forgejo is an optional portability target.

## Tracker organization

The baseline includes **13 newly opened workstream epics**, **102 new task issues**, and three preserved open issues (#4, #7, #8). Each new task defines an outcome, priority, prerequisite references and acceptance evidence. Parent issues contain GitHub task lists linking to their children. The creation integration did **not** attach GitHub-native sub-issue or blocked-by relationships; those require a separate authorized API operation. Textual dependency links are the present source of truth until native linking is verified.

| Workstream | Parent issue | Delivery purpose |
| --- | --- | --- |
| Governance and onboarding | [#9](https://github.com/ctalaveraw/openops-platform/issues/9) | Charter, local developer commands, review process, ADRs |
| Static anime application | [#10](https://github.com/ctalaveraw/openops-platform/issues/10) | Browser UI, data fixtures, search/filter, watchlist, localStorage |
| Quality engineering | [#11](https://github.com/ctalaveraw/openops-platform/issues/11) | Python validation, Playwright, accessibility, negative tests |
| Production HTTP container | [#12](https://github.com/ctalaveraw/openops-platform/issues/12) | Static HTTP server, non-root OCI, multiarchitecture optional |
| Portable CI/CD | [#13](https://github.com/ctalaveraw/openops-platform/issues/13) | GitHub Actions, CLI-owned tests, OCI publication, existing [#4](https://github.com/ctalaveraw/openops-platform/issues/4) |
| Software supply-chain security | [#14](https://github.com/ctalaveraw/openops-platform/issues/14) | Threat modeling, secret scanning, SBOM, image policy, Renovate |
| Zulip and ChatOps | [#15](https://github.com/ctalaveraw/openops-platform/issues/15) | Self-hosted collaboration, GitHub integration, Python notifications, backups, optional AI |
| Kubernetes/GitOps | [#16](https://github.com/ctalaveraw/openops-platform/issues/16) | Disposable cluster, Kustomize, one controller, digest promotion/rollback |
| Reliability and observability | [#17](https://github.com/ctalaveraw/openops-platform/issues/17) | Monitoring, synthetic checks, alerts, incident drill |
| Infrastructure/cloud | [#18](https://github.com/ctalaveraw/openops-platform/issues/18) | Host/network ownership, Ansible, optional AWS Terraform/OIDC |
| Recovery | [#19](https://github.com/ctalaveraw/openops-platform/issues/19) | Backup inventories, independent restores, DR evidence |
| Forgejo portability | [#20](https://github.com/ctalaveraw/openops-platform/issues/20) | Disposable forge rehearsal, secure runner, capability parity, migration decision |
| Deferred experiments | [#21](https://github.com/ctalaveraw/openops-platform/issues/21) | Linux automation workload, AI RAG, second application, portfolio demonstrations |
| Documentation website | [#8](https://github.com/ctalaveraw/openops-platform/issues/8) | MkDocs/Pages, link lint, handbook publishing |
| Developer experience parity | [#7](https://github.com/ctalaveraw/openops-platform/issues/7) | Provider-neutral event/API contracts, onboarding, porting acceptance |

## First usable vertical slice (P0)

The local application must work before a persistent platform is required:

1. [#22](https://github.com/ctalaveraw/openops-platform/issues/22) — ratify charter and scope; [#24](https://github.com/ctalaveraw/openops-platform/issues/24) — local bootstrap.
2. [#26](https://github.com/ctalaveraw/openops-platform/issues/26) — catalog JSON contract; [#29](https://github.com/ctalaveraw/openops-platform/issues/29) — Python catalog tests.
3. [#28](https://github.com/ctalaveraw/openops-platform/issues/28) — controlled JS/MicroPython comparison; [#30](https://github.com/ctalaveraw/openops-platform/issues/30) — runtime ADR.
4. [#31](https://github.com/ctalaveraw/openops-platform/issues/31), [#32](https://github.com/ctalaveraw/openops-platform/issues/32), [#36](https://github.com/ctalaveraw/openops-platform/issues/36), [#37](https://github.com/ctalaveraw/openops-platform/issues/37) — implement semantic UI, JSON fetch, search/filter and browser-local persistence.
5. [#34](https://github.com/ctalaveraw/openops-platform/issues/34) and [#41](https://github.com/ctalaveraw/openops-platform/issues/41) — Python Playwright acceptance.
6. [#4](https://github.com/ctalaveraw/openops-platform/issues/4), [#47](https://github.com/ctalaveraw/openops-platform/issues/47), [#48](https://github.com/ctalaveraw/openops-platform/issues/48) — the initial joint Markdown lint/CLI workflow and PR validation.

**Parallel, independent lane:** [#63](https://github.com/ctalaveraw/openops-platform/issues/63) verifies the installed Zulip organization; then [#64](https://github.com/ctalaveraw/openops-platform/issues/64) establishes webhook reachability, [#65](https://github.com/ctalaveraw/openops-platform/issues/65) delivers Python notifications, and [#68](https://github.com/ctalaveraw/openops-platform/issues/68) integrates GitHub events. This lane must not gate app development.

## Platform dependency spine

```text
Local app + Python tests
    -> static OCI image (#43, #44)
    -> GitHub Actions qualification/publish (#50, #51)
    -> disposable Kubernetes (#71, #74, #76)
    -> selected GitOps reconciler (#75, #80)
    -> reviewed digest promotion (#82)
    -> drift, failure and rollback proof (#84)
    -> observability and incident drill (#79, #85)
    -> documented recoverability (#94-#101)
```

Security runs alongside each stage: start with trust boundary [#55](https://github.com/ctalaveraw/openops-platform/issues/55), secret scanning [#56](https://github.com/ctalaveraw/openops-platform/issues/56), key/credential ownership [#60](https://github.com/ctalaveraw/openops-platform/issues/60), then artifact and policy evidence [#58](https://github.com/ctalaveraw/openops-platform/issues/58), [#59](https://github.com/ctalaveraw/openops-platform/issues/59), [#62](https://github.com/ctalaveraw/openops-platform/issues/62).

## Priority interpretation and ownership

- **P0:** unlock a functioning local app, a reproducible contributor experience and first GitHub/Zulip signal.
- **P1:** complete reliable OCI/CI/delivery, quality and immediate security boundaries.
- **P2:** strengthen operating posture, monitoring, backups, platform controls and documentation.
- **P3:** optional Forgejo rehearsal, AWS/hybrid experiments and second product. These do not block the MVP.

Issues are not automatically assigned to Ben, Alex or any other contributor merely because their skills align with a topic. Independent work, peer review and pairing are preferred over mandatory person-to-person dependencies. Original assignments on existing issues remain untouched.

## Maintenance

Issue states and blockers are authoritative at the issue itself; this page explains work structure, not a second Kanban. Update the dependency map after major replanning and link accepted decisions to reviewed ADRs. Avoid marking an epic complete because its checklist was created. Project evidence belongs with the implementing PR or runbook.

For architecture and portability gates, see [Roadmap and Acceptance](Roadmap-and-Acceptance.md), [Application Product](Application-Product.md), and [Capability Parity and DX](Capability-Parity-and-DX.md).
