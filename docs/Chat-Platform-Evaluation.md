# Team Chat Platform Evaluation

**Status:** Zulip selected for the current OpenOps collaboration rollout; installation in progress, acceptance tests not yet recorded  
**Reviewed:** 2026-11-18

## Decision context

Slack Free's limited searchable history and app limits motivated an independently operated collaboration service. The engineering requirements are durable searchable discussion, meaningful asynchronous topics, PR/CI alerts, Python-capable bots, an upgrade/backup path, and no paid chat subscription for the small team.

**Direction:** Self-hosted Zulip is the primary collaboration candidate being installed. Mattermost Team Edition remains an evaluated alternative, not a parallel deployment requirement. GitHub and GitHub Actions remain canonical for source and CI today; potential Forgejo migration is a distinct decision.

## Decision rationale

| Requirement | Zulip | Mattermost Team Edition | Current outcome |
| --- | --- | --- | --- |
| History outside Slack's free retention policy | Self-managed server and backups | Self-managed server and backups | Both viable |
| Asynchronous technical discussion | Topic-first model | Channels and threads | Zulip favored |
| GitHub PR/review notifications | Official Zulip GitHub integration | Webhook/plugin path | Zulip meets initial need |
| Developer-facing webhooks | Native integrations and Slack-compatible incoming payload | Incoming webhooks | Both viable |
| Python-native bot integration | REST API and interactive bot framework | REST/webhook/bot accounts | Zulip favored |
| Slack-like interface | Different channel/topic mental model | Closer UI | Mattermost favored |
| Voice/video huddles | External configured call provider such as Jitsi | Edition-specific calling features | Not a core OpenOps parity requirement |
| AI features | Optional local/remote model integration; verify installed version | Agents vary by license/edition | Pilot read-only; no assumed entitlement |
| Operational responsibilities | DB, files, web ingress, mobile push service, backups | Similar host/DB/backup obligations | Explicit acceptance gate |

Mattermost **Entry** and **Team Edition** are not interchangeable products and have different proprietary feature/license implications. Zulip does not automatically replicate Slack Workflow Builder, Canvas, arbitrary Marketplace apps, interactive Block Kit or Slack's proprietary API. Zulip supports Slack-*format* incoming webhooks, not general Slack-app execution.

## Initial Zulip operating model

Start with **one organization** for OpenOps and a modest channel/topic plan:

- **engineering:** architecture, PR design, frontend, CI/CD, Kubernetes, decisions in progress
- **development-events:** pull requests, reviews, CI failures, releases
- **operations:** deployments, incidents, availability, recovery
- **research:** tool evaluations, integrations, future work

One self-hosted Zulip server can host multiple organizations; a second organization is appropriate only for separate membership and governance. Independent organization spaces on one server still share compute, upgrades and failure domains.

The server must have verified TLS, appropriate access control, durable database/attachment/configuration backup, and a tested restoration procedure. Official mobile push notification service access is free for small self-hosted organizations up to ten users; document the external service dependency and eligibility rules.

## Acceptance tests (not yet complete)

1. Ben and the primary maintainer can use clients, search long topics and access permitted channels.
2. A GitHub PR and review event appears in a correctly routed Zulip topic with evidence links.
3. A portable CI notifier posts to a Zulip bot without a Slack application, and fails gracefully during Zulip outage.
4. Access-control, bot-token rotation, TLS, restore, and mobile notifications are validated.
5. A read-only Python bot can retrieve only explicitly authorized content if AI is enabled.

[Capability-parity contract](Capability-Parity-and-DX.md) is authoritative for provider mappings. [ChatOps and AI](ChatOps-and-AI.md) specifies event, security and bot boundaries.

## Sources

- [Zulip native integrations](https://zulip.com/help/integrations-overview)
- [Zulip GitHub integration](https://zulip.com/integrations/github)
- [Zulip self-hosting](https://zulip.com/self-hosting/)
- [Zulip call provider configuration](https://zulip.com/help/configure-call-provider)
