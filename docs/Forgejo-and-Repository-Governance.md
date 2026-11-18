# Forgejo and Repository Governance

**Status:** GitHub canonical today; Forgejo migration is optional and contingent on capability parity

**Reviewed:** 2026-11-18

## Canonical repository policy

**Current authority:** GitHub provides canonical source, issues, PRs, repository settings and GitHub Actions. **Potential target:** Forgejo offers independently operated Git/PR/CI capabilities if the team later elects a migration. GitHub becomes a read-only public mirror **only after** a verified, independently approved cutover. A Forgejo installation is not a reason to migrate by itself.

The GitHub repo for this project is `ctalaveraw/openops-platform`. No second independently writable `main` branch should exist after cutover.

## Roles and branch controls

Use short-lived feature branches, reviewed PRs, mandatory relevant CI checks, and scoped owner privileges. Establish an admin break-glass process that does not silently bypass review. Protect CI/workflow configuration with additional review because changes can alter runner permissions and the credential boundary.

Create issue labels for product, platform, security, documentation, investigation and incident work; avoid excessive bespoke workflow fields. An issue is a discrete implementation or experiment with verifiable acceptance, not a permanent planning document.

## Wiki versus source

The independent handbook repository is for continuously revised architecture context and evaluated alternatives. Long-lived accepted decisions affecting implementation should also have stable ADRs in source control, reviewed through PRs. Implemented runbooks and exact configuration belong with code. The handbook can link to those artifacts but should not create a second operational truth.

This handbook is a normal, separately maintained Git repository. Neither source-code mirroring nor a future native Forgejo wiki automatically captures its history. Mirror and back up the handbook explicitly; evaluate any later wiki import as a separate migration.

## Runner and forge trust

Forgejo Actions jobs execute code supplied by commits and pull requests. The runner is not inherently sandboxed against all hostile workflows merely because it is on a local VM. The forge database, repo storage, signing keys, registry credentials and production deployment identities must never be exposed to an untrusted PR runner. Separate publisher identities from validator identities.

## Federation and public contribution

Disabling GitHub issues/PRs simplifies canonical discussion at the cost of increasing friction for external contributors. Before disabling them, validate public Forgejo reachability, registration/invitation policy, spam moderation and contribution instructions. A GitHub mirror must plainly identify the canonical upstream and how to report issues or submit changes. This is a governance decision, not a mandatory configuration.

## Capability and operations gate

Before a migration decision, apply [Capability Parity and DX](Capability-Parity-and-DX.md): protected PR checks, review authority, isolated runner execution, artifacts, OIDC trust, provider-specific webhook routing and human-approved GitOps promotion must pass with documented differences. Forgejo Actions explicitly does not guarantee GitHub Actions compatibility; essential logic must remain in portable scripts. GitHub environment reviewer controls do not imply a native Forgejo equivalent; prove the required authorization outcome through alternative controls.

## Operations gate

Test account recovery, branch protection, organization/repo permissions, source import, issue/PR history, handbook operations, backup/restore and routine upgrade rollback before declaring Forgejo the only canonical forge.

References: [Forgejo repository mirror documentation](https://forgejo.org/docs/latest/user/repo-mirror/), [Forgejo Actions security](https://forgejo.org/docs/latest/admin/actions/security/).
