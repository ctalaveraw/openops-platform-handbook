# Observability, Incidents and Reliability

**Status:** Proposed operations model

**Reviewed:** 2026-11-17

## Monitoring layers

| Layer | Signal | Initial mechanism |
| --- | --- | --- |
| Browser | Render/search errors, failed fetch, user-visible behavior | Python-driven browser acceptance tests and browser console capture |
| Static server | HTTP response and request failures | Access logs and HTTP synthetic checks |
| Container | Restarts, resource budget, readiness | Kubernetes events and metrics |
| Delivery | CI failures, image identity, rollout status | Forgejo workflow status, registry digest checks, GitOps conditions |
| Platform | DNS, TLS, registry, forge, disk, database, runner | Prometheus exporters, service probes and availability checks |
| Recovery | Failed backup, restore time and independent operation | Explicit drills and measured recovery results |

Application JavaScript exceptions do not automatically appear in container logs. Test/browser instrumentation must address browser-only behavior. Avoid a frontend telemetry service before it has an operational purpose and data-retention policy.

## Alert quality

Alert on actionable symptoms or loss of monitoring coverage, not on every raw metric. Every alert should identify owner, severity, evidence link, immediate check, expected behavior and recovery procedure. Test both failure and recovery transitions and verify routing during chat unavailability.

Use a small number of alerts initially: static site unreachable, Forgejo unavailable, failed protected-branch build, GitOps reconciliation stalled, unavailable ready replicas, and backup verification failure. Expand only when the team has runbooks to handle the alerts.

## Incident process

1. Record occurrence, observed impact, affected scope and an initial timeline.
2. Preserve logs/events and source/release identities before making changes where safe.
3. Mitigate with the narrowest supported action; record any break-glass changes.
4. Restore desired Git state and confirm runtime convergence.
5. Verify service behavior independently of controller status.
6. Capture cause, detection, response, recovery evidence, contributing design weaknesses and follow-up issues.

## Controlled exercises

Intentionally deploy an invalid digest in a disposable environment; introduce a synthetic HTTP failure; suspend GitOps reconciliation; break a monitoring target; simulate a failed registry pull; restore from a known backup. Rehearse diagnostics, not just a happy-path build.

## Reliability contract

A release is not successful merely because a Kubernetes Deployment reached its desired replica count. Both artifact identity and an external HTTP acceptance test are required. Service continuity on a one-replica RollingUpdate depends on actual rollout configuration and readiness and must be verified, not assumed.

Metrics and logs are not an audit trail by themselves; publish incident summaries and validated evidence in canonical source/issue history.
