# Architecture Decision Register

**Status:** Working decision inventory

**Reviewed:** 2026-11-18

This page summarizes unresolved and provisional choices. It does not replace reviewed ADRs in the source repository. An *accepted* entry requires an ADR identifier, author, reviewers, alternatives, rationale, consequences and an implementation gate.

| ID | Question | Current leaning | Status | Evidence required |
| --- | --- | --- | --- | --- |
| D-001 | Initial app runtime | Vanilla browser JS + Python tooling; compare MicroPython | Candidate | Equivalent UI tests, runtime/build measurement |
| D-002 | Static HTTP server | Caddy; compare BusyBox/nginx-unprivileged | Candidate | Size, behavior, hardening and logs |
| D-003 | Canonical forge | GitHub now; optional future Forgejo | Current operating direction | Portability acceptance and separately approved cutover |
| D-004 | Public mirror | GitHub as read-only publication | Candidate | Refs parity, failure and token-rotation test |
| D-005 | Team chat | Zulip selected for current installation | Direction selected; live acceptance pending | TLS, webhook, UX, mobile push, retention and restore tests |
| D-006 | CI execution | GitHub Actions today; isolated Forgejo runner only for future rehearsal | Current direction | Same CLI tests plus provider-specific isolation, OIDC and artifact tests |
| D-007 | Dependency updates | Self-hosted Renovate | Candidate | Scoped token, limited PR and audit test |
| D-008 | Security baseline | Gitleaks, image scan; evaluate SAST/SBOM | Candidate | Synthetic positive/negative and exceptions |
| D-009 | Release authority | One GitOps reconciler, approved digest promotion | Architectural target | Drift, rollback, RBAC and identity evidence |
| D-010 | GitOps implementation | Flux vs Argo CD | Open | Team familiarity, controller footprint and permissions |
| D-011 | Infrastructure lifecycle | Terraform for cloud; Ansible for host convergence | Candidate | Disjoint resource ownership, rebuild and teardown |
| D-012 | AI agent | Read-only, opt-in bot with model selection | Candidate | Authorization, data and injection controls |
| D-013 | External contribution | Public GitHub issues/PRs or redirect to Forgejo | Open | Accessibility, moderation and contributor test |
| D-014 | Server automation product | Separate follow-on workload | Backlog concept | Read-only pilot and security model |
| D-015 | Provider portability and DX | Required capability parity, not identical interfaces | Proposed acceptance contract | [Capability matrix](Capability-Parity-and-DX.md) and [DX issue #7](https://github.com/ctalaveraw/openops-platform/issues/7) pass on both stacks |

## Recording a decision

Document the problem, non-negotiable requirements, alternatives, experiments, risks, selected choice, rejected tradeoffs, update and rollback mechanisms, and evidence needed to invalidate the decision. Promote to `docs/adr/NNNN-*.md` in the canonical source repo when the interface is acted upon. Link the ADR here; do not keep two contradictory authoritative copies.

## Architectural invariants independent of choices

- The app remains locally runnable without a persistent backend or infrastructure.
- The production artifact remains immutable between qualification and deployment.
- Sensitive runner and deployment identities are separate.
- Chat and AI are optional; failures do not block core delivery/recovery.
- The GitHub mirror never becomes a second independent source authority after cutover.
- All accepted features preserve recoverability and a documented upgrade path.
