# Backup, Restore and Disaster Recovery

**Status:** Recovery requirements proposed; no RTO/RPO achieved claim

**Reviewed:** 2026-11-17

## Data inventory

| Asset | Risk if lost | Backup expectation |
| --- | --- | --- |
| Forgejo repositories and independent handbook | Code, handbook history, non-mirrored refs | Independent verified Git backup |
| Forgejo database | Issues, PRs, accounts, comments, metadata | Consistent logical/physical backup matched to app version |
| Forgejo attachments/LFS/packages | Supporting evidence and binary artifacts | Explicit state backup with integrity verification |
| Registry | Qualified OCI artifacts and release lineage | Image export or mirrored immutable distribution plus restore test |
| Chat database and uploads | Collaboration history and evidence | Encrypted, documented per-version restoration |
| Configuration, TLS, secrets | Ability to reconstruct services and identities | Controlled encrypted backup; separate key recovery |
| Terraform state | Infrastructure lineage and managed resource mapping | Durable, locked, versioned encrypted storage and independent recovery |

A public GitHub code mirror is not a complete backup. A copied database without compatible uploads/configuration may not restore usable service state. A backup that has never been restored is an unverified hypothesis.

## Recovery architecture

Keep off-host copies outside the failure domain of the primary service. Maintain recovery instructions usable without the chat service, GitOps controller, or the forge itself. Protect recovery credentials against loss of the same machine or account that holds the encrypted backup.

## Restore exercises

- Reconstruct a fresh Forgejo instance and confirm Git refs, protected branches, issues/PRs, wiki, attachments and account access.
- Recover an OCI digest and compare identity with the qualified release record.
- Restore chat with files and user context, verify search/history behavior.
- Recreate the app independently and verify a known-good endpoint.
- Validate an intentionally failed or partial backup is detected.

## Objectives

Do not promise an RTO or RPO until systems are classified by criticality and repeated drills establish measured values. Record start/end times, recovered datasets, backup age, lost writes, unexpected dependencies and operator effort.

## GitHub mirror role

A GitHub push mirror is useful for source visibility and a secondary copy of selected Git refs. It is not a write-independent backup if an accidental or malicious force update propagates. Preserve immutable snapshots and separate restore credentials.

## Runbook acceptance

A contributor other than the original administrator can execute a restore from documented prerequisites without relying on undocumented local state. A recovery exercise must not risk corrupting the live installation.
