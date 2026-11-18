# Infrastructure and Environments

**Status:** Candidate deployment plan

**Reviewed:** 2026-11-17

## Environment ladder

| Stage | Location | Purpose | Required to develop the app? |
| --- | --- | --- | --- |
| Local | Engineer workstation | Serve static assets, run Python tests, debug UI | Yes |
| Integration | Disposable VM/kind | Test image, manifests, negative cases and runner wiring | No |
| Homelab | Separately managed persistent services | Exercise Forgejo, registry, chat, GitOps, monitoring, backups | No |
| Cloud | AWS target when scheduled | Validate Terraform, identity, network, cost and portability | No |

Do not require every engineer to have the entire homelab. Each stage must offer a documented standalone validation path and deterministic local fixtures.

## Self-managed service grouping

Forgejo and its database have persistent state. Chat and its database/attachments have independent persistence and retention requirements. The runner executes potentially hostile code and must be isolated from both. The registry stores qualified artifacts independently. A cluster may host app workloads, but failure of the cluster must not prevent forge recovery. Avoid an all-in-one Compose deployment as the long-term security and disaster-recovery model.

An initial loopback-only Compose pilot on a disposable VM can validate interoperability. It is an experiment, not an internet-ready service. Do not expose it without TLS, access control, secrets management, restore tests and network policy.

## Provisioning boundaries

Terraform or an equivalent tool may manage cloud compute, network and foundational infrastructure. Ansible can converge host packages, configuration and managed services. Kubernetes bootstrapping and GitOps own separate responsibilities. Explicitly record which tool is authoritative for each resource; do not put the same configuration under two competing reconcilers.

Keep provider-specific state, identity, backend configuration and locking outside the browser app. Test plan/apply/destroy lifecycle only in appropriately isolated environments. Require a cost estimate, budget controls and teardown instructions before introducing chargeable AWS resources.

## Networking and ingress

Design DNS, TLS, public/private reachability, outbound webhook delivery, NAT and access policy before exposing the forge. Webhook receivers should authenticate requests where the producer supports signatures/secrets. Runner egress and registry access should be narrower than administrative service access.

## Storage and recovery

Define persistent volume ownership per service; avoid anonymous named volumes when backup requirements are not known. Document clean bootstrap, upgrade, state backup and restore for each service. Recovery relies on off-host copies of repository state, database and configuration, not only GitHub code mirroring.

## Environment acceptance

A disposable environment can be created without modifying personal/production services, can deploy the qualified image by digest, can run positive/negative tests, and can be removed without orphaned cloud resources. The persistent environment adds backup, access and monitoring gates.
