# Zulip integration map

**Status:** Live integration inventory and implementation plan  
**Reviewed:** 2026-11-20  
**Implementation authorities:** [OpenOps Platform](https://github.com/ctalaveraw/openops-platform) and [ARM Homelab Platform](https://github.com/ctalaveraw/arm-homelab-platform)

This page records how developer activity, CI, services and monitoring reach the OpenOps Zulip organization. It is not a deployment record. Native integrations, locally tested code and confirmed live deliveries are distinguished below.

## System boundaries

- GitHub remains canonical for both `openops-platform` and `arm-homelab-platform`. Their Forgejo repositories are not independent places for pull requests or reviews.
- Forgejo is operational on the private LAN with an ARM64 runner and OCI registry. It is not exposed through the public Zulip ingress.
- Zulip 13.2 is hosted behind Caddy and Cloudflare Tunnel. The OpenOps collaboration organization and the separate personal Projects organization have distinct accounts, channels and bot credentials.
- Gotify 3.1.1 is LAN-only. It is already a notification endpoint, not an event forwarder to Zulip.
- The latest homelab service inventory reported Prometheus and Grafana stopped. Do not describe an alerting integration as operational merely because dashboards or Compose files exist.

Never write a personal Projects token or organization URL into an OpenOps workflow, or vice versa. Confirm the exact organization origin before any live test.

## Integration inventory

| Source | Destination and topic | Transport | Verified status / next test | Work |
| --- | --- | --- | --- | --- |
| OpenOps GitHub PR events | `development-events`, native repository/PR topic | Zulip native GitHub webhook | PR #128 arrival observed; reviews, issues and filtering pending | [OpenOps #68](https://github.com/ctalaveraw/openops-platform/issues/68) |
| OpenOps GitHub CI failures | `development-events` / `CI / Repository checks` | Checked-in Python notifier and Generic bot | CLI, fixture and offline HTTP tests merged; live trusted job pending | [OpenOps #65](https://github.com/ctalaveraw/openops-platform/issues/65) |
| ARM GitHub PR/issue activity | OpenOps collaboration channel; repository-specific topics | Zulip native GitHub webhook | Proposed; choose selected events and verify no duplicate notifications | [OpenOps #15](https://github.com/ctalaveraw/openops-platform/issues/15) |
| Private Forgejo mirror | None for mirrored Git pushes | No webhook | **Intentionally suppressed**; source activity already comes from canonical GitHub | [OpenOps #107](https://github.com/ctalaveraw/openops-platform/issues/107) |
| Forgejo-specific runner/build or registry failure | Operations channel/topic to be agreed | Forgejo Slack-format webhook where sufficient; portable Python sender for CI | Not integrated; test actual Forgejo 15.0.11 event coverage | [OpenOps #107](https://github.com/ctalaveraw/openops-platform/issues/107) |
| Gotify | Operations channel/topic to be agreed, grouped by application | Client-token polling adapter with persistent ID cursor | Proposed in the homelab repository; no live bridge | [ARM #39](https://github.com/ctalaveraw/arm-homelab-platform/issues/39) |
| Uptime Kuma | Availability/recovery topic per monitored service | Version-verified native notification or small adapter | Proposed; choose only high-value down/recovered events | To be scoped |
| Grafana / Alertmanager | Infrastructure alert topics by service | Zulip native Grafana or Alertmanager integration | Proposed; resume and verify monitoring capacity first | To be scoped |
| Flux / Kubernetes / app rollout | `Deployments / <app>` topic in operations channel | Alertmanager / existing monitoring first; adapter only if needed | Proposed; alert on failed reconciliation/rollout and verified recovery | To be scoped |
| New parallel repositories and services | Topic rooted in repository, service or incident identifier | Native integration, existing notifier or a small adapter | Add through the onboarding record below | To be scoped |

**Topic policy:** Zulip topics group conversations; they should identify a stable PR, workflow, application, service or incident. Do not create a new topic for every ordinary commit or metric scrape. New channel names in the table are proposals until created and tested; `development-events` and the CI topic are already present in OpenOps test fixtures.

## Routing rules

1. **One canonical source per event.** GitHub supplies PR, review and issue activity while it is the source of truth. The Forgejo mirror does not post a second copy.
2. **Native first.** Use Zulip's native GitHub, Grafana or compatible incoming integrations where the required events and topic layout work.
3. **Keep the proven notifier narrow.** OpenOps `scripts/notifications/notify_zulip.py` accepts only CI failure and recovery events from GitHub or Forgejo. Do not silently turn its version-1 schema into a generic Gotify or monitoring envelope.
4. **No operational dependency on chat.** CI, GitOps, uptime monitoring and recoverability remain independent when Zulip is unavailable. Report notification failure separately.
5. **Event evidence is a link, not a copied log.** Include the source repository, run/incident URL, revision or image digest, and an event identifier. Never copy secrets, private logs or credentials into chat.
6. **Failure and recovery are paired.** Send failed/degraded and confirmed recovered states when possible. Routine green CI and ordinary successful deployments are silent.
7. **No automatic write-back.** Messages and optional AI bots cannot merge PRs, edit GitOps desired state or run commands against hosts unless a separately reviewed security design approves that authority.

## Current OpenOps CI contract

The version-1 JSON envelope is defined and tested in the implementation repository: [notifier documentation](https://github.com/ctalaveraw/openops-platform/blob/main/docs/ci/zulip-notifications.md) and [provider fixture](https://github.com/ctalaveraw/openops-platform/blob/main/examples/notifications/github-failure.json).

Required fields are `schema_version`, `source=ci`, `provider` (`github` or `forgejo`), `event` (`validation_failed` or `validation_recovered`), `repository`, `workflow`, a full hexadecimal `revision`, `severity`, `idempotency_key`, credential-free HTTPS `evidence_url`, and `destination.channel`/`destination.topic`. Recovery additionally requires `recovery_of`.

The Python command retries bounded HTTP 429 responses, refuses redirects and unexpected organization origins, and requires a privately stored Generic bot configuration. The identifier supports correlation, **not guaranteed exactly-once delivery**.

Local preview (no bot token or network access):

```sh
make bootstrap
.venv/bin/python scripts/notifications/notify_zulip.py \
  --event examples/notifications/github-failure.json
```

GitHub Actions secrets `ZULIP_BOT_CONFIG` and `ZULIP_SITE` were operator-reported as configured. Their values must not be copied into the handbook. A trusted-workflow live send and failure-preserving behavior remain acceptance work in [#65](https://github.com/ctalaveraw/openops-platform/issues/65).

## Rollout sequence

### 1. Complete GitHub activity verification

The operator observed a native PR-opened notification for OpenOps PR #128 in `development-events`. Use ordinary project activity, not synthetic issues or PRs, to verify review submission, selected issue event, expected topic and hyperlink. Check the GitHub webhook delivery record and remove any duplicate event selection. The selected event policy is already documented in [#69](https://github.com/ctalaveraw/openops-platform/issues/69), with follow-up in [#68](https://github.com/ctalaveraw/openops-platform/issues/68).

### 2. Connect CI results without changing CI authority

Keep Zulip credentials exclusively in the trusted notification step. Invoke the checked-in Python notifier only after validation reaches a definitive failure state; do not expose those credentials to fork PRs or execute PR-controlled notification code with secrets. Test a known failure and confirm that Zulip delivery failure never masks or replaces the underlying CI result.

### 3. Extend native GitHub activity to the ARM repo

Use a dedicated webhook configuration for the canonical ARM GitHub repository, targeting the collaboration organization. Initially select PR/review/issue events only. Verify normal event delivery, topic mapping and no duplication with OpenOps. Do not subscribe to routine `push` on both the canonical repo and its Forgejo mirror.

### 4. Test local Forgejo-only events

From the LAN-side Forgejo repository, test a Slack-format Forgejo webhook against a **Zulip Slack-compatible incoming webhook** and a scoped bot. Limit events to operations that exist locally and are not already announced by GitHub. Forgejo's notification channel adapters do not support all event types; use the actual version-15.0.11 `Test Delivery` and recent-delivery records to verify coverage. For unsupported CI events, call the existing Python notifier from a trusted Forgejo workflow rather than exposing a generic listener or public Forgejo endpoint. Use its separate credentials, runner and network boundary.

### 5. Complete Gotify forwarding independently

Do not duplicate the work in [ARM #39](https://github.com/ctalaveraw/arm-homelab-platform/issues/39). Use a Gotify **client** token for reading; an application token only sends notifications. The bridge should persist the last confirmed Gotify message ID, process new messages oldest-first, and move its cursor only after Zulip confirms delivery. A lost acknowledgement can still cause an identifiable duplicate. Keep it separate from Gotify and Zulip systemd lifecycles.

### 6. Add monitoring and future services incrementally

First verify the actual monitoring processes and resource headroom. Then connect one Uptime Kuma down/recovered pair and one Grafana/Alertmanager firing/resolved pair before enabling broad alerts. Every new service must have an owner, an origin, an event list, a Zulip organization/channel/topic, credential location, duplicate policy and failure test. Add it to the table only after a successful and a negative-path result.

## Integration acceptance and operating safety

- A canonical GitHub PR produces one linked Zulip discussion, not duplicate GitHub/Forgejo mirror announcements.
- A protected CI failure produces a link to the failing run, and a chat outage leaves the CI result unchanged.
- A Forgejo-specific event reaches the correct organization without exposing Forgejo publicly.
- Gotify forwards a non-sensitive test notification after normal startup, restart and temporary destination outage, without silently skipping messages.
- OpenOps bot tokens cannot post into the personal Projects organization.
- A failed or expired credential produces a safe error; no URL containing secrets appears in Git, workflow logs, issue comments or screenshots.
- A restored deployment documents what notification state, message cursor and credentials must be recovered separately.

## References

- [Native Zulip GitHub integration](https://zulip.com/integrations/github)
- [Forgejo webhook types and delivery testing](https://forgejo.org/docs/latest/user/repository/webhooks/)
- [Zulip Slack-compatible incoming webhook](https://zulip.com/integrations/slack)
- [Zulip Grafana integration](https://zulip.com/integrations/grafana)
- [Zulip Prometheus Alertmanager integration](https://zulip.com/integrations/alertmanager)
- [ARM Zulip operations](https://github.com/ctalaveraw/arm-homelab-platform/blob/main/docs/runbooks/zulip-controller.md)
- [ARM Forgejo identity and URLs](https://github.com/ctalaveraw/arm-homelab-platform/blob/main/docs/runbooks/forgejo-reference-map.md)
