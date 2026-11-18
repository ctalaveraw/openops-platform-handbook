# ChatOps, Webhooks and Optional AI

**Status:** Proposed provider-neutral integration contract; Zulip deployment and endpoints not yet verified  
**Reviewed:** 2026-11-18  
**Tracking:** [OpenOps DX issue #7](https://github.com/ctalaveraw/openops-platform/issues/7)

## Provider roles

- **Canonical forge now:** GitHub. Official Zulip GitHub integration supplies PR/review/issue events.
- **CI now:** GitHub Actions. Trusted steps invoke the same repository-owned Python/CLI notification code used locally.
- **Optional future forge:** Forgejo. Its webhook payloads and runner events require version-tested integration or an adapter; the event contract, destinations and bot remain unchanged.
- **Collaboration:** one self-hosted Zulip organization with channels/topics rather than a Slack-compatible app platform.
- **AI:** optional read-only consumer, never deployment authority.

Use native integrations first. Introduce a small Python adapter only for missing event types, validation, routing or formatting. Avoid an always-running event service if direct webhook posts are sufficient.

## Signal policy

| Event | Destination | Action |
| --- | --- | --- |
| PR opened / review requested | development-events / Pull requests | Notify with direct review link |
| CI failure on protected branch | development-events / CI failures | Include revision, run URL, component and failure state |
| Artifact promotion or release | development-events / Releases | Identify immutable digest and provenance |
| GitOps reconciliation or deployment failure | operations / Deployments | Include source desired-state link, severity and recovery owner |
| Recovery after prior incident | operations / Incidents | Correlate with original event |
| Successful routine push, scrape or job | None by default | Avoid notification spam |

## Portable event contract

```json
{
  "schema_version": 1,
  "source": "ci",
  "provider": "github",
  "event": "validation_failed",
  "repository": "openops-platform",
  "revision": "example-commit-sha",
  "severity": "warning",
  "idempotency_key": "ci:example-run:failure",
  "evidence_url": "https://example.invalid/run/123",
  "destination": {"channel": "development-events", "topic": "CI failures"}
}
```

This is an **internal logical envelope**, not a Zulip or forge wire format. Adapt only at the producer/transport edge. Prefer durable event identifiers, explicit retries, bounded messages, redacted logs and structured topic mapping. Duplicate handling is an explicit test, not a magical delivery guarantee.

## Integration and network boundaries

GitHub webhook POST delivery requires reachable HTTPS ingress to a narrow Zulip integration endpoint; alternatively, an authorized worker with outbound connectivity relays events. Do **not** expose the administrative UI or internal service ports solely to satisfy a webhook. Verify authenticity: native Zulip incoming webhook credentials or a signature-validation adapter for raw provider events. Rotate credentials and record least privilege.

Zulip supports Slack-compatible incoming webhook payloads with formatting limitations. This **does not** run arbitrary Slack apps, slash-command modals or Marketplace SaaS integrations.

CI or GitOps may continue when Zulip is down. Notification failure is visible but never causes an otherwise valid build, release or rollback to fail unless a separate reviewed release policy explicitly requires acknowledgement.

## Optional engineering assistant

An opt-in Python bot can answer questions about authorized handbook pages and linked CI/incident evidence, summarize a chosen discussion topic, and draft an issue. Begin read-only and mention-triggered; never give it secret-manager, production Kubernetes, merge, workflow approval or arbitrary command-execution permissions. Enforce user-level retrieval authorization; treat retrieved chat/log/code content as untrusted. Record model endpoint, retention, cost, auditing, and incident response. Chat summaries are non-authoritative until reviewed into an ADR or issue.

## Verification checklist

- Official GitHub integration delivers review and issue events with sensible topics.
- Synthetic failure and recovery notifications have correct evidence links and no token leakage.
- Invalid credentials, replay/duplicates, rate limits, unreachable service and malformed payloads have bounded behavior.
- A Forgejo test emitter produces equivalent *outcomes* through a verified adapter; do not assume GitHub webhook schema is interchangeable with Forgejo.
- Zulip database, uploads and settings restore independently of GitHub/Forgejo state.
- The CLI notifier runs locally and from both CI providers with identical test fixtures.

See [Capability Parity and DX](Capability-Parity-and-DX.md) and [CI and Supply Chain](CI-and-Supply-Chain.md).
