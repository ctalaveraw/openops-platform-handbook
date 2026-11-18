# Security, Trust Boundaries and Secrets

**Status:** Threat-model baseline; controls to be implemented incrementally

**Reviewed:** 2026-11-17

## Threat boundaries

The important boundaries are: contributor/PR input to runner; runner to forge; builder to artifact registry; promotion bot to Git; reconciler to Kubernetes; chat/webhook source to bot; AI text to tools; and off-host backups to recovery credentials.

## Core controls

| Boundary | Design requirement |
| --- | --- |
| Untrusted PR code | Disposable isolated runner, no production tokens, no host Docker socket on a trusted service host |
| Trusted publish workflow | Explicit narrow credentials; verify build inputs and transferred artifact identity |
| Git and PR policy | Protected branch, reviewed changes, independent security review for workflows/identity |
| Artifact | Pinned versions/digests, scanning, SBOM, attestation/signature validation when adopted |
| GitOps | Least-privilege service account for owned namespace/resources only |
| Static web | Non-root container, minimal server, safe headers, text-safe rendering, CSP where compatible |
| Webhooks | Authenticate or validate origin/signature where supported; rate-limit and handle replay/duplicates |
| Secrets | No credentials in Git, wiki, chat, logs, browser bundles or untrusted runners |
| AI | Restricted context, no implicit tool authority, human approval for mutation, injection tests |
| Backups | Encrypted, off-host, independently restorable, restricted credentials and key recovery |

## Progressive implementation

Use an Observe → Warn → Block → Verify sequence. Begin with a baseline and synthetic positives/negatives. Make exceptions explicit with owner, justification, scope, expiry and compensating control. Block high-confidence failures once false-positive handling and a recovery path are tested. Avoid permanent scanner suppressions.

## Supply-chain ownership

A self-hosted tool still depends on upstream patches, signatures, vulnerability information and distribution. Maintain an internally retrievable, pinned copy of critical runner images/actions/binaries and a documented upgrade workflow. An internal copy is not evidence of source audit or safety.

## Separation of duties

Untrusted PR validation must not share identity with registry publishing, infra mutation, production promotion or backups. A chat bot may request a future approved operation but must not be the sole authorizer or directly hold high-scope credentials. Terraform state credentials and Kubernetes GitOps credentials are independently scoped.

## Evidence requirements

Show a test that denies an untrusted fork secret, a denied namespace operation, a rejected fake secret, a blocked tampered artifact when signing is enabled, a webhook authentication failure, and a tested key/credential rotation procedure. No page should claim a security control is active until a test demonstrates it.

## Related reading

- [Forgejo Actions security](https://forgejo.org/docs/latest/admin/actions/security/)
- [ARM platform risk-based DevSecOps ADR](https://github.com/ctalaveraw/arm-homelab-platform/blob/main/docs/adr/0013-devsecops-policy-and-promotion.md)
