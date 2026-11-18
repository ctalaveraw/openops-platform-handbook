# Engineering Governance and Collaboration

**Status:** Proposed working agreement

**Reviewed:** 2026-11-17

## Roles

| Function | Primary responsibility | Review expectation |
| --- | --- | --- |
| Project architect, repository owner, planning and day-to-day integration | Chris | Shares design context; accepts or rejects scope changes with recorded rationale |
| Assisting engineer, Kubernetes and infrastructure collaboration | Ben | Owns independent implementation tasks; cross-reviews platform decisions |
| Optional application/frontend contributor | Alex | Contributes UI behavior and reviews browser design where available |

Responsibilities describe current coordination, not exclusive technical domains. Every contributor should be able to complete a small feature end-to-end. Ben is not a prerequisite for progress; tasks can be done independently, in parallel, or by pairing.

## Work lifecycle

1. Document a desired outcome and smallest verifiable interface.
2. Open an issue that contains scope, acceptance tests, and dependencies.
3. Create a short-lived feature branch from the canonical default branch.
4. Implement and run relevant local checks.
5. Open a PR linked to the issue; include evidence and risk notes.
6. Obtain review from another contributor where available; document deliberate single-maintainer exceptions.
7. Merge after required checks; verify the observed result.
8. Close the issue with evidence, update an ADR or handbook page if the decision changed.

Avoid using chat threads as substitute issue trackers. Do not create broad issues that combine unrelated product, runner, DNS and cloud changes.

## Review expectations

PRs must identify the change, intended behavior, tests performed, rollback considerations, and any sensitive changes to credentials, workflows, privileges or deployment ownership. Reviewer checks should focus on boundaries, failure handling, portability and maintainability, not only formatting.

Protected branches, reviewer requirements, automation identities and repository roles will be recorded when Forgejo governance is tested; policy is not assumed deployed.

## Parallel work without blocking

Contract-first tasks enable independent progress: the application exposes static assets and a fixed HTTP route; the delivery system consumes an OCI image; telemetry consumes HTTP status and Kubernetes metrics; the browser test suite targets documented UI behavior. Each component can use local fixtures or a disposable environment.

## Knowledge-sharing process

Decisions that change architecture become concise ADRs. Research and alternatives live in the handbook. Technical questions are resolved with an experiment and its evidence. Repo discussions and PR reviews remain canonical in the chosen forge. Ben's [git-mirror-syncer](https://github.com/Kwamebond/git-mirror-syncer) copies a Git branch using fast-forward-only rules; it does not synchronize issues, PRs, comments, settings or a wiki. Treat it as a personal read-only context aid, not another source of project authority.

## Meetings and milestones

Sprints are work units, not fixed deadlines. Review an artifact when a gate is reached rather than inventing a schedule. End each milestone with a demo, an evidence link, a new backlog priority, and an explicit statement of what remains incomplete.
