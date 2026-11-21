# ChatOps and optional AI

**Status:** Operational Zulip and partial GitHub integration; additional integrations in progress  
**Reviewed:** 2026-11-20  
**Tracking:** [OpenOps Zulip epic #15](https://github.com/ctalaveraw/openops-platform/issues/15), [ARM Gotify bridge #39](https://github.com/ctalaveraw/arm-homelab-platform/issues/39)

The [Zulip integration map](Zulip-Integration-Map.md) is the current inventory, event-routing policy, operational acceptance checklist and rollout order for OpenOps, the ARM homelab, both Git forges and future services. This page records the longer-term design boundaries.

## Role of Zulip

Zulip is a communication and incident coordination destination. It does not own source control, run CI, deploy applications or retain the only copy of incident evidence. Issues, PRs, source commits, immutable artifacts, monitoring databases and reviewed runbooks remain in their existing systems of record.

Zulip 13.2 runs in the ARM homelab behind public HTTPS ingress. The OpenOps collaboration organization is distinct from the personal Projects organization, including memberships and bot credentials. The operator has confirmed an OpenOps PR-opened event from the native GitHub integration. Review and issue event coverage, CI delivery, monitoring and recovery remain separately tracked.

## Integration responsibility

| Component | Responsibility | Status |
| --- | --- | --- |
| GitHub incoming webhook | Human project activity: selected PR, review and issue events, grouped by native topic | First OpenOps PR notification observed; remaining events under test |
| Python CI notifier | Selected failure and correlated recovery events from GitHub and Forgejo | Implemented, offline tested; trusted workflow sending pending |
| Forgejo-specific integration | Local CI, registry or forge activity not already reported by canonical GitHub | Planned and version-dependent |
| Gotify forwarding | Operational notifications already received by the LAN-only Gotify service | Separate ARM implementation planned |
| Monitoring integrations | Service health, alert firing and resolution, deployment trouble | Not enabled as a complete live integration |
| AI assistant | Optional explanations and summaries of authorized sources | Research only |

The current OpenOps sender's strict version-1 schema covers **only** CI failure/recovery, not arbitrary service events. New event producers can use native Zulip integrations or independently versioned adapters; do not silently change the existing contract.

## Authority and failure boundaries

1. GitHub is canonical until a reviewed migration explicitly changes that choice. Do not mirror GitHub PR and push notifications a second time from Forgejo.
2. Each producer uses its own least-privileged credential. Separate organizations require separate bot configurations and exact destination URL checks.
3. External webhook authentication must be validated by the native Zulip integration or a narrowly scoped signature-validation adapter. Treat webhook payloads, log lines and hyperlinks as untrusted input.
4. Chat delivery is best-effort. A failed notification cannot change a passing or failing validation result, hold a GitOps rollout, or stop recovery.
5. Pair meaningful failures with confirmed recoveries. Suppress routine successful builds, noisy pushes, scrapes and repetitive service reports.
6. A reusable message includes an identifiable source, event identifier, clear result and a link to the original evidence. Do not paste secret-bearing logs or internal-only links into a public channel.

## Optional AI assistant

Start with a read-only, manually invoked Python bot. Candidate tasks are citing handbook pages, summarizing a chosen authorized topic or explaining a supplied sanitized CI failure. Maintain the sender's organization and channel permissions; do not let an AI bot access unrelated private organizations through a broadly privileged account.

Do not grant production host shells, `kubectl`, secret-manager access, PR merge rights or deployment approval as an initial capability. Retrieved code, chat and logs are untrusted input. Model provider, prompt retention, access-control tests, error behavior and human review requirements must be recorded before deployment.

A bot response is not an accepted architecture decision, incident record or deployment instruction until a maintainer reviews and records it in the appropriate system.

## References

- [Zulip integration map](Zulip-Integration-Map.md)
- [Capability parity and developer experience](Capability-Parity-and-DX.md)
- [OpenOps CI sender and tests](https://github.com/ctalaveraw/openops-platform/blob/main/docs/ci/zulip-notifications.md)
- [Zulip REST API](https://zulip.com/api/rest)
- [Zulip integrations](https://zulip.com/help/integrations-overview)
