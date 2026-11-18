# Forgejo Migration and GitHub Mirroring

**Status:** Planned, not executed

**Reviewed:** 2026-11-17

## Target topology

Forgejo becomes the canonical source for Git, issues, PRs, releases and runner orchestration. This separately versioned handbook remains independently maintained and backed up. GitHub publishes a read-only project copy for visibility, discovery and optional code distribution. Human changes occur against one canonical forge.

## Preflight

Inventory all Git refs/tags, branch protection, issues, PRs, reviews, comments, wiki Git history, LFS, attachments, releases, webhooks, actions/secrets and package dependencies. Decide what must be migrated, archived or intentionally left historical. Record repository identifiers and a rollback period before freezing any writes.

## Migration stages

1. Back up the source and its wiki separately; export metadata and repository settings.
2. Install a test Forgejo instance with configured HTTPS, identities, access policy and backups.
3. Import a disposable repository and independently verify protected branches, issues, PRs, wiki and permissions.
4. Migrate the project and validate every required object class; inspect handbook page history and cross-links.
5. Announce a cutover window and freeze writes at the old canonical endpoint.
6. Confirm Git refs/tags, review/issue history, account rights and CI ownership in Forgejo.
7. Configure the GitHub destination as publication-only with a narrowly scoped, expiring credential.
8. Enable push mirroring after alignment; verify a test commit appears on GitHub with exact SHA.
9. Remove unauthorized alternate writers and publish a canonical upstream/contribution link.
10. Test restore and failure behavior before retiring the old source infrastructure.

## Mirroring limitations

Forgejo's push mirroring may force-update the destination. It is inappropriate as a substitute for an immutable backup. Git mirroring does not inherently migrate issues, pull requests, comments, wiki repos, package registries, CI settings or chat. Review all individual data types during cutover.

A mirror may fail when tokens expire or remote protection blocks updates. Monitor mirror errors; never grant broad admin scope merely to suppress failures. A GitHub fine-grained PAT is one possible credential method; validate its exact required permissions and rotation procedure in a test repository.

## Contributor experience

Decide whether GitHub issues and pull requests remain open as a public intake channel or are disabled with a canonical Forgejo link. Keeping two independently active discussion systems produces fragmentation; disabling the public one may reduce community accessibility. Document the choice as governance, not a default script step.

## Exit criteria

No dual-writer `main`; matching expected Git refs; preserved or explicitly archived issue/wiki state; working contribution path; controlled runner/secret migration; monitored public mirror; verified off-host restore; documented rollback.

Reference: [Forgejo mirroring](https://forgejo.org/docs/latest/user/repo-mirror/).
