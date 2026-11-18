# Project Charter

**Status:** Proposed charter, pending team review

**Reviewed:** 2026-11-17

## Objective

Build an approachable application and a defensible self-managed platform that demonstrates modern DevOps, platform engineering, software delivery, collaboration, operations, and security practices. The application provides a realistic workload; it does not justify unnecessary application complexity.

## Desired outcomes

- A usable product that can be developed without cloud access or a Kubernetes cluster.
- A small, understandable production artifact with a documented HTTP interface and observable failure modes.
- Repeatable, reviewed CI from source revision to qualified OCI artifact.
- Declarative release promotion with a single GitOps reconciler and tested recovery.
- Portable infrastructure definitions and an owned forge/runner/registry path.
- Evidence that the system can be reproduced by an engineer other than its original implementer.
- Practical experience with issues, feature branches, review, and cross-functional learning.

## Constraints

Application priorities, in order: independently executable; maintainable; browser-rendered; minimal backend requirements; simple debugging; small runtime dependency surface; small image; testability; native browser APIs; language familiarity. Python is preferred for tooling and tests, but must not force an unnecessarily complicated browser runtime. A full CPython browser environment or server-side Python stack requires explicit justification.

Platform priorities: self-hostable reproducibility, least privilege, vendor independence, no dependence on a marketplace app for a critical workflow, and incremental implementation. GitHub and public services may remain distribution or collaboration endpoints; they cannot be the only recovery copy of critical operational state.

## Initial scope

A static anime catalog/watchlist; Python-driven tests; OCI packaging; protected PR workflow; CI/security baseline; a disposable Kubernetes deployment; reviewed digest promotion; reconciliation tests; basic telemetry and incident evidence.

## Explicit exclusions from the initial release

User accounts, persistent server databases, distributed application microservices, service mesh, custom operator, central developer portal, multiple production clusters, autonomous AI deployments, and a multi-cloud abstraction. These are optional future exercises, not prerequisites for a first usable product.

## Success criteria

A new contributor can clone the repo, install documented local tooling, serve the app, reproduce tests, and open a reviewable PR. The qualified container can run without the build toolchain. A failed release can be detected and reversed from documented Git state. A complete recovery path is demonstrated on an isolated system before it is described as operational.

## Portfolio evidence

Record one or more of: exact source revision, commands used, relevant logs, test outputs, policy failures, digest comparisons, reconciliation state, before/after observations, and a recovery verification. Separate experiments from completed production-like outcomes.

## References

- Existing principles and evidence: [ARM Homelab Platform](https://github.com/ctalaveraw/arm-homelab-platform)
- Implementation repository: [openops-platform](https://github.com/ctalaveraw/openops-platform)
- Engineering handbook: [openops-platform-handbook](https://github.com/ctalaveraw/openops-platform-handbook)
