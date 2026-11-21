# Capability Parity and Developer Experience

**Status:** Proposed portability contract; product capabilities researched, end-to-end parity unverified  
**Reviewed:** 2026-11-20  
**Current authority:** GitHub and GitHub Actions  
**Current collaboration:** Self-hosted Zulip 13.2; the OpenOps organization and first native GitHub PR notification are operator-confirmed; remaining events, CI delivery and recovery are not yet fully verified  
**Optional future target:** Forgejo, Forgejo Actions and the same Zulip deployment  
**Tracking:** [OpenOps DX issue #7](https://github.com/ctalaveraw/openops-platform/issues/7)

## Decision question

Can Zulip + GitHub Actions replace Slack + GitHub Actions now, while preserving the *required engineering experience* under Zulip + Forgejo Actions later?

**Answer:** Yes for a deliberately scoped OpenOps development, CI, notification and GitOps workflow, given portable tooling and a small number of adapters. **No for universal 1:1 capability parity across all Slack/GitHub products.** Slack's proprietary application surface and some GitHub Actions protections, artifact operations and hosted services have no automatic, native equivalent. Defining required outcomes and verifying them on both providers is the engineering objective.

Capability parity is about outcomes, not identical UI, action syntax, event payloads, API models or operational cost. A feature is not “portable” merely because both systems advertise a webhook.

## Compatibility vocabulary

| Classification | Definition | Evidence |
| --- | --- | --- |
| **Native** | Provider supplies a supported feature meeting the requirement directly | Configuration and live pass/fail test |
| **Adapter** | Provider-neutral logic plus a small, documented boundary integration meets the requirement | Same contract tests against both providers |
| **Alternative control** | Different mechanism meets the security or operational outcome, with documented behavioral differences | Threat model and negative-path tests |
| **Gap / excluded** | No accepted equivalent, or capability explicitly outside the OpenOps baseline | Approved scope decision; no false parity claim |
| **Unverified** | Documented capability not yet exercised on the deployed instance/version | Test owner, fixture, result and follow-up |

The mappings below are research-based design hypotheses, **not** operational test results.

## Capability map: collaboration and ChatOps

| Required capability / Slack baseline | GitHub + Zulip now | Forgejo + Zulip later | Class / qualification |
| --- | --- | --- | --- |
| Channels, DMs, searchable collaboration | Zulip channels, topics, DMs, self-managed history | Unchanged Zulip | **Native**, different threading/UX |
| Long-lived knowledge | Zulip search plus canonical handbook/ADRs | Unchanged | **Native + policy**; chat is not an incident database backup |
| PRs, reviews, issue notifications | Official Zulip GitHub webhook (PR-opened event observed) | Forgejo Slack-compatible notification or verified adapter, tested on deployed version | **Partial live acceptance now; additional events unverified** |
| CI failure and recovery messages | Trusted CI job invokes portable notifier / Zulip API | Same notifier from isolated Forgejo runner | **Adapter**, no embedded Slack app dependency |
| Slack-format incoming notifications | Zulip Slack-compatible incoming webhook | Same | **Native subset**; formatting quirks and no Slack app lifecycle |
| Bot commands, status lookup and summaries | Zulip REST API, Python interactive bots, optional local LLM | Same | **Adapter**; scoped read-only bot, not Slack OAuth/modals |
| Slack Workflow Builder, interactive Block Kit, arbitrary Marketplace apps | Separate CLI/scripts/automation only for required workflows | Same | **Gap / replace selectively**, not a general emulator |
| Slack Canvas / Slack-native Lists / Huddles | Handbook, Zulip topics/collaborative tools and external call provider | Same | **Alternative UX**; no pixel/feature-for-feature claim |
| Slack AI features | Optional Zulip AI integration or custom Python bot | Same | **Conditional**: model licensing, deployed version and data policy |
| Mobile push notifications | Zulip self-hosted push service free for up to ten users; eligibility rules for more | Same | **External dependency / policy**; test if required |
| Alerts from monitoring | Grafana/Alertmanager or small notifier to Zulip | Same | **Adapter**, chat outage must not stop monitoring |

## Capability map: source, delivery and operations

| Required outcome | GitHub / GitHub Actions now | Forgejo / Forgejo Actions later | Class / qualification |
| --- | --- | --- | --- |
| Branches, commits, protected review | Git and GitHub PR checks/rules | Git and Forgejo PR checks/branch protection | **Native**, policy parity must be tested |
| PR tests and matrices | GitHub Actions invoking checked-in tools | Forgejo Actions invoking the **same** tools | **Native + adapter** for workflow definitions/context |
| Scheduled dependency proposals | Pinned Renovate CLI on trusted job | Same Renovate CLI with Forgejo platform adapter | **Adapter**; provider API/token changes |
| Secret scanning, SAST and browser checks | Pinned local CLI/OCI scripts | Unchanged scripts | **Portable** if not tied to hosted SaaS APIs |
| OCI build, SBOM, signature and scanning | External owned registry and pinned tools | Same registry/tools | **Portable**; verify digest and provenance |
| Job-to-job artifacts | GitHub Actions artifacts; prefer OCI/generic store for durable outputs | Forgejo artifacts within a workflow | **Conditional**; Forgejo documentation says cross-workflow artifact access is currently unavailable |
| Caching | Runner cache where configured | Forgejo runner cache where configured | **Optional optimization**; never an artifact transfer guarantee |
| Reusable workflows/actions | GitHub-native workflows/actions | Forgejo supports reusable workflows/actions with differences | **Adapter**; vendor/source pinning plus test |
| CI execution capacity | GitHub-hosted or self-hosted runners | Self-operated, isolated Forgejo runners | **Different operational responsibility**; no free hosted-runner promise |
| CI secrets, token scopes and trusted jobs | GitHub policies and permissions | Forgejo tokens/runner rules; GitHub keys may be ignored | **Alternative controls**; test trust boundary, do not assume YAML permissions imply least privilege |
| OIDC cloud authentication | GitHub OIDC claims/trust policies | Forgejo Actions OIDC with different enablement and token claims | **Native but provider-specific trust**; dedicated policy for each issuer |
| Required human deployment approval | GitHub environments / reviewers or reviewed Git change | Reviewed protected Git desired-state change before GitOps reconcile | **Alternative control**; not automatic equality with GitHub environment UI gates |
| CI workflow and incident history | GitHub run pages + retained evidence | Forgejo run pages + independently stored evidence | **Adapter**; retention and API limits differ |
| GitOps promotion and rollback | Reviewed digest bump, one reconciler, revert to known digest | Identical GitOps contract | **Portable** if neither CI provider directly mutates live workloads |
| Source-mirror portability | Current GitHub canonical | Optional Forgejo canonical + GitHub read-only code mirror | **Migration**; source mirror does not carry issues, PRs, wiki, secrets, artifacts, or Zulip state |

### Documented provider differences that must be tested

Forgejo explicitly states that Actions is familiar to GitHub Actions, **not fully compatible**. Its documentation describes different default runner environments; missing GitHub context fields; ignored job keys including some permission settings; and different OIDC enablement. Forgejo supports OIDC, but the trust policy must use its actual issuer, subject and audience claims. Forgejo artifacts cannot currently be accessed from a different workflow run, so use an independent immutable OCI/artifact store for inter-run handoff when needed.

GitHub deployment environments can require reviewers, restrict deployment branches, and gate access to environment secrets. Forgejo must reproduce the **required approval and authority outcome** via explicit reviewed GitOps promotion and protection policies; do not promise exact GitHub environment UI behavior.

Forgejo v17 release notes changed/deprecated webhook event families for Action runs. Prefer version-tested **workflow-run/job events**, not a hardcoded older event format, when creating the future adapter.

## Target architecture: stable center, thin provider edges

```text
                    GitHub (canonical today)
                  /         |             \
           PR/issues      Actions        GitOps config
                |             |               |
       native Zulip hook      v               v
                |       scripts/ci/*       protected PR
                |       tests/scans       promotion digest
                |           |                 |
                |       OCI registry        GitOps controller
                |           |                 |
                +--- event envelope ---+      workloads
                                       |
                                Zulip notifier
                                       |
                                Zulip topics

  Future: Forgejo replaces only forge-side edges.
  Python/CLI contracts, images, application and Zulip stay unchanged.
```

In production, GitHub's webhook delivery must reach a controlled HTTPS ingress route on the self-hosted Zulip server, or an approved outbound relay must be used. Do not expose internal admin interfaces, CI secrets or privileged endpoints merely to receive notifications. Zulip notification delivery is **best-effort** and is not a release prerequisite.

### Stable CI event envelope (implemented)

The [OpenOps notifier](https://github.com/ctalaveraw/openops-platform/blob/main/scripts/notifications/notify_zulip.py) implements a strict version-1 **CI-only** JSON event envelope. Its canonical [GitHub fixture](https://github.com/ctalaveraw/openops-platform/blob/main/examples/notifications/github-failure.json) and [contract documentation](https://github.com/ctalaveraw/openops-platform/blob/main/docs/ci/zulip-notifications.md) take precedence over older illustrative designs.

```json
{
  "schema_version": 1,
  "source": "ci",
  "provider": "github",
  "event": "validation_failed",
  "repository": "example/openops-platform",
  "workflow": "Repository checks",
  "revision": "0123456789abcdef0123456789abcdef01234567",
  "severity": "warning",
  "idempotency_key": "github:repository-checks:123:1:failure",
  "evidence_url": "https://github.com/example/openops-platform/actions/runs/123",
  "destination": {
    "channel": "development-events",
    "topic": "CI / Repository checks"
  }
}
```

The emitter creates the envelope from a trusted CI context. The notifier validates it, formats a Zulip message and sends through an exact-organization Generic bot only when explicitly requested. Current tests cover both GitHub and Forgejo fixtures; trusted-workflow live delivery and durable deduplication remain open. Other operational events should use native Zulip integrations or an independently reviewed adapter, not an undocumented expansion of the CI schema.

For the current channel, topic, event ownership and rollout state, see the [Zulip integration map](Zulip-Integration-Map.md).

## DX quality bar

- A fresh clone works locally with no forge, chat service, Kubernetes cluster or paid API.
- Local commands perform the actual lint, test, scan, build and smoke operations. CI runs those commands; provider workflow YAML handles only wiring and permissions.
- Contributors can locate an issue, create and review a PR, understand a failed check and follow links from chat to original evidence.
- Secret-bearing or privileged publication steps never execute untrusted PR code; CI and GitOps use separate identities.
- All required third-party actions/tools are pinned, licensed, mirrored or have documented fallback; no invisible hosted Marketplace SaaS dependency.
- An outage of Zulip/AI does not block CI, artifact qualification, deployment, rollback, monitoring persistence or documented incident response.
- A repo mirror is never treated as the sole backup; Zulip messages, attachments, forge metadata, registry and handbook have independent restore paths.
- Portability requires real test results on both providers, not logical equivalence asserted from documentation.

## Incremental implementation roadmap

| Stage | Deliverable | Exit evidence |
| --- | --- | --- |
| **P0: Scope** | Inventory required flows, capability/gap matrix, provider boundary, source authority | Reviewed charter and contract fixtures |
| **P1: Zulip now** | GitHub PR/review webhook, one portable CI-failure notification, scoped topic routing | Positive and negative tests, ingress/auth review, offline behavior |
| **P2: Portable CI** | Versioned Python/CLI scripts for tests, scanning, OCI and notifications | Same local and CI commands, immutable digest and verification evidence |
| **P3: Forgejo rehearsal** | Disposable forge and isolated runner with webhook, token/OIDC and release exercises | Pass/fail matrix, documented adapter changes and gaps |
| **P4: Portability gate** | ADR and manual cutover plan if desired | Required outcomes pass both stacks, backups restored, rollback proven |

The tracking issue is [openops-platform#7](https://github.com/ctalaveraw/openops-platform/issues/7). Each stage should be decomposed into independently reviewable issues as implementation begins; this page remains the architecture and test contract.

## Primary references

- [Zulip GitHub integration](https://zulip.com/integrations/github)
- [Zulip native integrations and Slack-compatible webhooks](https://zulip.com/help/integrations-overview)
- [Zulip self-hosting, exports and push policy](https://zulip.com/self-hosting/)
- [Zulip call providers](https://zulip.com/help/configure-call-provider)
- [Forgejo webhook types](https://forgejo.org/docs/latest/user/repository/webhooks/)
- [Forgejo Actions compared with GitHub Actions](https://forgejo.org/docs/latest/user/actions/github-actions/)
- [Forgejo Actions reference and security](https://forgejo.org/docs/latest/user/actions/reference/)
- [Forgejo artifact and cache limitations](https://forgejo.org/docs/latest/user/actions/advanced-features/)
- [Forgejo OIDC identity](https://forgejo.org/docs/latest/user/actions/security-openid-connect/)
- [Forgejo 17 workflow webhook changes](https://forgejo.org/2026-10-release-v17-0/)
- [GitHub deployment environments](https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments)
