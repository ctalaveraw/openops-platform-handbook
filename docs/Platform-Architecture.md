# Platform Architecture and Ownership Boundaries

**Status:** Target design, not deployed

**Reviewed:** 2026-11-17

## Logical flow

```text
Engineers -> canonical Forgejo -> PR/branch policy -> isolated validation runner
                                                   -> qualified OCI image
                                                   -> immutable registry digest
                       -> approved promotion change -> GitOps repository
                                                   -> Flux / Argo CD
                                                   -> Kubernetes workload
                        \-> outbound GitHub publication mirror
Forgejo / CI / GitOps / monitoring -> webhook or adapter -> team chat
Optional read-only AI bot -> scoped chat/forge interfaces and selected model
```

## Ownership contracts

| Domain | Routine owner | Explicit non-owner |
| --- | --- | --- |
| Source revision, issues, PRs | Canonical forge | Public mirror |
| Artifact qualification | CI in isolated runner | Chat bot or operator laptop |
| OCI immutable objects | Artifact registry | GitOps controller |
| Desired deployed revision | Approved Git change | Container rebuild workflow |
| Kubernetes application resources | One GitOps reconciler with scoped identity | CI publisher, second reconciler |
| Host bootstrap | Provisioning/IaC and OS convergence tooling with agreed boundaries | Application repository |
| Discussions and notifications | Chat service | Incident source of truth |
| AI assistance | Optional scoped integration | Autonomous operator by default |

The same verified artifact must be promoted through environments without being rebuilt. A digest reference is the operational identity, not the mutable tag used at build time.

## Reliability boundaries

The application and its static HTTP serving must remain usable if chat, AI, the public mirror, or the CI control plane becomes unavailable. Running workloads should not depend on the forge staying online. Recovery of the forge must not require a live runner hosted only inside that forge.

Implement one integration boundary at a time. A source-code outage is different from an application outage; alerting and escalation should distinguish them.

## Portability contract

Developers can run the application locally. Local kind or equivalent can verify Kubernetes behavior. A homelab environment provides a persistent reference deployment. A later AWS target validates portable provisioning and deployment interfaces. Only environment-specific configuration (endpoints, storage classes, ingress/DNS, identity and credentials) should change, not application source or release identity.

## Reference design versus reference implementation

The existing [ARM Homelab Platform](https://github.com/ctalaveraw/arm-homelab-platform) has demonstrated CI qualification, immutable OCI publication, Flux reconciliation, metrics and controlled failure recovery. This project borrows proven contracts, not the whole ARM-specific stack or its deployment authority. Verified work in the ARM repository must not be represented as completed work in this repository.

## Design anti-patterns

- Application tightly coupled to Kubernetes/Flux/Forgejo APIs without a product requirement.
- Runner on the same trusted host with unrestricted Docker socket and service credentials.
- Multiple controllers concurrently updating the same workloads.
- Rebuild-per-environment promotion that changes the qualified artifact.
- Operator-only setup steps without tracked declarative state or recovery.
- Self-hosting every supporting service before one useful application and CI test exist.
