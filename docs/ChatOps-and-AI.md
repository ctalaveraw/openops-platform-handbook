# ChatOps, Webhooks and Optional AI

**Status:** Architecture proposals and acceptance criteria

**Reviewed:** 2026-11-17

## Notification objectives

Deliver high-signal engineering events without routing service operations through chat. Examples: PR review requested, protected-branch CI failure, published release digest, promotion success/failure, reconciliation error, service unavailable, and incident recovery. Avoid per-commit spam, every successful scrape, and unbounded log dumps.

## Integration progression

1. Use native Forgejo notifications or direct supported webhooks where possible.
2. Send CI status notifications from trusted workflow steps, keeping errors in CI logs as the authoritative evidence.
3. Use Grafana/Alertmanager contact points or a documented small adapter for operational alerts.
4. Introduce a shared Python formatter only when multiple producers require consistent filtering, routing or message schema.
5. Later add bot commands that retrieve read-only state using minimal scopes.

Do not build a persistent central notification service before a simple webhook pilot demonstrates a need.

## Candidate event envelope

```json
{
  "schema_version": 1,
  "source": "gitops",
  "environment": "dev",
  "component": "anime-app",
  "event": "deployment_failed",
  "severity": "warning",
  "revision": "example-revision",
  "evidence_url": "https://example.invalid/incident"
}
```

This is an internal candidate contract, not the payload sent unmodified to Slack/Mattermost/Zulip. A transport adapter converts it to the destination format. Include correlation identifiers and timestamps when implementation makes them necessary; avoid secrets, raw tokens, privileged log output or personally identifying data.

## Slack-like integration

Mattermost supports incoming webhooks and bot accounts with a familiar channel model. Zulip supports incoming integrations and topic-specific messages, including Python-friendly bot development. Verify the exact Forgejo webhook type, authentication/header options, payload compatibility, and failure/retry semantics in the chosen products. Avoid trusting arbitrary inbound events without verification.

## AI assistant design

An optional model-backed bot may summarize linked incident evidence, answer questions about approved public project documentation, explain CI failure patterns and suggest issue drafts. Start with human-invoked, **read-only** capabilities and no credentials for Kubernetes mutations, deployment approvals or secret stores. Human approval remains mandatory for any future write capability.

Before connecting source or chat history, define data classification, retention, training/provider policy, prompt injection boundaries, retrieval scope, model endpoint, availability, cost limits, audit logging and denial behavior. A self-hosted model is a possible implementation, not an automatic guarantee that all data and dependencies are local.

## Acceptance evidence

Demonstrate a webhook delivered once, a failing endpoint, bounded retry behavior, safe secret storage, lack of fatal impact when chat is offline, and read-only bot authorization tests. Establish whether duplicated events are acceptable before adding deduplication state.
