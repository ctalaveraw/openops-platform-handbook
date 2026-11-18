# Team Chat Platform Evaluation

**Status:** Decision open; Zulip and Mattermost Team Edition shortlisted

**Reviewed:** 2026-11-17

## Evaluation context

The team first considered Slack Free with GitHub integration and outgoing notifications. Short searchable history, deletion policy and hosted-service dependence motivate a self-managed alternative. Chat is for coordination and ChatOps, not permanent system state.

## Comparison

| Requirement | Mattermost Team Edition | Zulip self-hosted |
| --- | --- | --- |
| Familiar Slack-like channel/DM UX | Closer match | Different channel + topic model |
| Incoming webhooks | Supported; Slack-format compatibility | Supported; Slack-compatible incoming integrations |
| Bot/API model | Webhooks, bot accounts, slash commands, plugins | REST API, outgoing webhook bots, Python tooling |
| Durable discussion organization | Channels and threads | Topic-based conversation model is central |
| Self-managed deployment | Open-source Team Edition, PostgreSQL | Open-source server, more involved stack |
| AI integration | Agents features and licensing need edition-specific verification | Bot/model integration; local model possible |
| License/feature caveat | Distinguish Team Edition from commercial Entry/other tiers | Check exact deployed version and integration capabilities |

Mattermost Entry and Mattermost Team Edition are not interchangeable. Do not assume the AI features or history policies advertised for Entry are available in Team Edition. No ChatOps/AI feature should rely on an unverified edition entitlement.

## Operational evaluation

Run both on isolated test instances with test data. Evaluate desktop/mobile workflow; search after aging/backup; webhook delivery from Forgejo; actionable PR links; bot command ergonomics; Python API usage; upgrade procedure; database/attachment backup; restore; RAM/disk consumption; and license/feature behavior.

## Proposed weighting

1. Free self-hosted long-term data access and predictable licensing.
2. Forgejo webhooks and standard ChatOps integration without proprietary SaaS bridges.
3. Usability for daily code reviews and incident discussion.
4. Bot/API support, especially Python.
5. Resource usage and restore complexity.
6. Optional local AI support without privileged automation.

A familiarity preference alone should not decide the result; conversely, topic organization has limited value if contributors dislike the UX. Document an experiment and select one chat platform through a reviewed ADR.

## Architecture rule

The forge, CI, GitOps and application must function during chat outages. The chat service receives notifications rather than acting as the authoritative deployment controller or sole incident log.

## References

- [Mattermost Team Edition](https://docs.mattermost.com/product-overview/editions-and-offerings.html)
- [Mattermost integration documentation](https://developers.mattermost.com/integrate/webhooks/incoming/)
- [Zulip integration documentation](https://zulip.com/integrations/)
- [Zulip self-hosting documentation](https://zulip.readthedocs.io/en/latest/production/install.html)
