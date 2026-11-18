# GitOps and Release Lifecycle

**Status:** Target architecture, not yet demonstrated in this project

**Reviewed:** 2026-11-17

## Control-plane authority

The CI system produces and qualifies artifacts. It must not routinely mutate application Deployments. A reviewed Git change specifies the desired image digest and configuration. One GitOps reconciler applies that state using a scoped identity. Choose either Flux or Argo CD as the routine owner for a given workload; do not run both against the same objects.

## Release sequence

1. A protected source change passes tests and review.
2. A trusted build produces one OCI image; the exact image is tested and scanned.
3. The image is published and retrieved independently by immutable digest.
4. A promotion PR changes the desired digest in an environment-specific Git declaration.
5. An authorized reviewer approves promotion after policy checks.
6. The reconciler observes the approved commit, changes only owned resources, and reports state.
7. Health checks verify the running digest, HTTP behavior, readiness, and dependency reachability.
8. Operational evidence links commit, CI run, digest, desired-state revision and runtime observation.

No routine `kubectl set image`, direct CI `kubectl apply`, or mutable-image-tag promotion should exist alongside GitOps ownership. Define a documented, scoped break-glass exception for recovery.

## Kubernetes minimal workload

The initial static app requires a Namespace policy/ownership decision, Deployment, Service and possibly an Ingress or Gateway. Use non-root execution, drop unnecessary capabilities, bound CPU/memory, a readiness check that reflects the ability to serve assets, and a liveness check only when useful. Prefer raw Kustomize overlays initially; introduce Helm when shared packaging requirements justify it.

## Reconciliation experiments

- Introduce out-of-band replica drift, observe correction and record the acting controller.
- Suspend reconciliation, confirm drift persists, resume and confirm restoration.
- Promote a nonexistent digest in an isolated environment, confirm rollout failure and old-service availability where strategy permits.
- Revert the desired-state commit and verify the exact qualified digest is restored.
- Test authorization that permits workload reconciliation but denies namespace and secret administration.

## Multi-environment design

Separate dev and later cloud-specific parameters into overlays or values, not application code. A release must be traceable to the same OCI digest in every environment. Environment overlays must not be free-form copies that silently diverge in security constraints.

## Failure semantics

A successful GitOps reconciliation is not proof of a healthy application. Record both desired-state convergence and application endpoint validation. A failed deployment should create an incident/evidence trail; chat notification is auxiliary, not the only record.

## Reference

The [ARM Homelab Platform](https://github.com/ctalaveraw/arm-homelab-platform) documents prior Flux experiments and verified rollback. The new project must reproduce and record its own evidence rather than inherit those status claims.
