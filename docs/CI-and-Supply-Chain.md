# Portable CI and Software Supply Chain

**Status:** Planned delivery contract; no implementation asserted

**Reviewed:** 2026-11-17

## Design rule

A workflow file orchestrates steps; it must not be the only place where essential behavior exists. Prefer a checked-in script invoking a pinned CLI or OCI image. The same validation should run locally when practical. Dependence on a proprietary marketplace integration is prohibited for an essential gate.

## Proposed tool selection

| Control | Candidate tooling | Required proof |
| --- | --- | --- |
| Python/data tests | stdlib/pytest, Ruff when useful | Reproducible clean checkout and failure fixture |
| Browser tests | Playwright Python | Basic interaction and console-error tests |
| Secret detection | Gitleaks, TruffleHog as alternative | Test credential fixture rejected; exception recorded |
| SAST | Semgrep Community CLI | Tested rules and known false-positive handling |
| Dependency updates | Self-hosted Renovate using dedicated Forgejo platform | Bounded, reviewable PRs; restricted token |
| Release notes | git-cliff or equivalent | Deterministic output from a known revision/tag |
| Container scanning | Trivy or Grype | Tested image/digest; findings policy |
| SBOM | Syft or tool-native SPDX/CycloneDX | Artifact linked to exact digest |
| Build | BuildKit/Buildx or compatible OCI builder | Re-pull exact qualified digest |
| Attestation/signature | Evaluate Sigstore/cosign and independent verification | Reject altered/untrusted artifact in test |

These are candidates, not mandatory simultaneous dependencies. Introduce scanners when their coverage and failure handling are understood; duplicate tools are not inherently stronger security.

## Pipeline contract

```text
Feature branch -> PR validation -> reviewed merge
    -> build immutable artifact -> test exact artifact -> scan/record evidence
    -> publish to owned registry -> record digest
    -> reviewed desired-state update -> reconciler deploys digest
    -> runtime verification and observable rollback path
```

Untrusted PR jobs must not receive publication, cloud, registry administration or production deployment credentials. Trusted publication jobs must validate the source and artifact they receive and use limited credentials. Artifact transfer between jobs must preserve digest identity; downloading and rebuilding elsewhere is not promotion.

## Action and dependency ownership

Pin actions by immutable commit, images by digest and CLI versions by checksum. Maintain source license, upstream URL, version, mirror location, update owner, verification method and emergency fallback. Internal mirroring protects availability; it does not eliminate the need to patch vulnerable upstream components. Treat Forgejo Actions and GitHub Actions syntax as similar but not identical; validate locally against the chosen runner implementation.

## Dependency automation

Run Renovate as a controlled, scheduled task with a dedicated Forgejo token and narrow repository permissions. Require PR review; do not grant blanket auto-merge initially. Make update runs observable and bounded, and rehearse the no-network path before claiming full offline reproducibility.

## Security progression

Start with observed findings and deterministic positive/negative tests. Add warning and exception handling with owner/expiry; enforce blocking on agreed critical issues; re-test that controls reject malicious inputs. An attestation is not proof of a secure build without independent trust validation.

References: [Renovate Forgejo platform](https://docs.renovatebot.com/modules/platform/forgejo/), [Forgejo runner security](https://forgejo.org/docs/latest/admin/actions/security/).
