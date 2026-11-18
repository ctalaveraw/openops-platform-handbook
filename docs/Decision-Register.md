# Architecture Decision Register

**Status:** Working decision inventory

**Reviewed:** 2026-11-17

This page summarizes unresolved and provisional choices. It does not replace reviewed ADRs in the source repository. An *accepted* entry requires an ADR identifier, author, reviewers, alternatives, rationale, consequences and an implementation gate.

| ID | Question | Current leaning | Status | Evidence required |
| --- | --- | --- | --- | --- |
| D-001 | Initial app runtime | Vanilla browser JS + Python tooling; compare MicroPython | Candidate | Equivalent UI tests, runtime/build measurement |
| D-002 | Static HTTP server | Caddy; compare BusyBox/nginx-unprivileged | Candidate | Size, behavior, hardening and logs |
| D-003 | Canonical forge | Self-managed Forgejo | Direction agreed for evaluation | Import, review and restore proof |
| D-004 | Public mirror | GitHub as read-only publication | Candidate | Refs parity, failure and token-rotation test |
| D-005 | Team chat | Zulip vs Mattermost Team Edition | Open | UX, webhook, API, license, retention and restore test |
| D-006 | CI execution | Isolated Forgejo runner | Candidate | PR/secret isolation tests and published digest |
| D-007 | Dependency updates | Self-hosted Renovate | Candidate | Scoped token, limited PR and audit test |
| D-008 | Security baseline | Gitleaks, image scan; evaluate SAST/SBOM | Candidate | Synthetic positive/negative and exceptions |
| D-009 | Release authority | One GitOps reconciler, approved digest promotion | Architectural target | Drift, rollback, RBAC and identity evidence |
| D-010 | GitOps implementation | Flux vs Argo CD | Open | Team familiarity, controller footprint and permissions |
| D-011 | Infrastructure lifecycle | Terraform for cloud; Ansible for host convergence | Candidate | Disjoint resource ownership, rebuild and teardown |
| D-012 | AI agent | Read-only, opt-in bot with model selection | Candidate | Authorization, data and injection controls |
| D-013 | External contribution | Public GitHub issues/PRs or redirect to Forgejo | Open | Accessibility, moderation and contributor test |
| D-014 | Server automation product | Separate follow-on workload | Backlog concept | Read-only pilot and security model |

## Recording a decision

Document the problem, non-negotiable requirements, alternatives, experiments, risks, selected choice, rejected tradeoffs, update and rollback mechanisms, and evidence needed to invalidate the decision. Promote to `docs/adr/NNNN-*.md` in the canonical source repo when the interface is acted upon. Link the ADR here; do not keep two contradictory authoritative copies.

## Architectural invariants independent of choices

- The app remains locally runnable without a persistent backend or infrastructure.
- The production artifact remains immutable between qualification and deployment.
- Sensitive runner and deployment identities are separate.
- Chat and AI are optional; failures do not block core delivery/recovery.
- The GitHub mirror never becomes a second independent source authority after cutover.
- All accepted features preserve recoverability and a documented upgrade path.
