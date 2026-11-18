# Handbook Maintenance Standard

**Status:** Editorial policy for this handbook

**Reviewed:** 2026-11-17

## Purpose and audience

The handbook is a maintained engineering reference for contributors and future operators. It captures plans, tradeoffs, architectural reasoning and research that are not yet executable implementation. Readers must be able to distinguish an idea from a tested fact without reading the project's chat history.

## Editorial rules

- Write in direct technical prose. Do not include conversational introductions, assistant self-reference, hype, emojis, praise, motivational language, or transcript-style dialogue.
- Prefer a precise claim, rationale, constraint or testable statement over broad aspirational prose.
- Rewrite each page as a complete coherent article when information changes. Do not append chronological AI output, copied conversation threads or repetitive dated updates.
- Remove obsolete advice or clearly mark it deprecated with a link to the superseding decision. Keep links and navigation consistent.
- Distinguish **Proposed**, **Candidate**, **Accepted (ADR)**, **Implemented**, **Verified**, and **Deprecated**. `Implemented` means code/config exists; `Verified` requires reproducible observed runtime evidence.
- Separate source-derived facts, current repository observations, and recommendations. Cite primary product docs or repository evidence for important external behavior.
- Use short tables only where they clarify comparisons. Avoid decorative formatting, redundant checklists and repeated summaries.
- Never include secrets, access tokens, private logs, full configuration dumps containing credentials or personal chat transcripts.
- Technical examples must state whether they are illustrative or runnable; no copied unverified AI-generated YAML should be presented as deployed configuration.

## Change workflow

1. Inspect the current handbook Git state and relevant source/ADR/issue state.
2. Identify the articles affected by the new evidence.
3. Replace managed article content in full; preserve unrelated pages.
4. Review the whole diff for contradictions, duplicated scope, broken page references and changes to implementation status.
5. Commit with a subject describing what changed; keep documentation changes in the handbook repository.
6. Publish without force-push and verify remote commit and link.
7. When a proposal is accepted for implementation, create an ADR and actionable issue in the canonical forge.

## Separation of concerns

| Artifact | Content |
| --- | --- |
| Wiki | Options, decisions in progress, target architecture, research and concepts |
| Source ADR | Accepted decisions and revision history |
| Issue | Task ownership and acceptance criteria |
| PR | Change diff and review |
| Runtime evidence/runbook | Commands, observations, recovery and tested implementation |
| Chat | Ephemeral coordination and notifications |

## Review cadence

Review at each milestone completion, after a significant external product/license change, or whenever a verified experiment invalidates an assumption. The review date describes editorial inspection, not proof that all referenced external products have been revalidated that day.

## Publishing safety

Edit `docs/index.md` and each affected article as complete, coherent documents. Preserve unrelated pages and Git history; review the full diff before pushing. The handbook remains a separate repository from the platform implementation.

## Static site build

The source of truth is Markdown under `docs/`. `mkdocs.yml` defines navigation and the optional site build. `mkdocs build --strict` must pass before published releases. The current handbook repository is not the native GitHub Wiki; it can be migrated independently to Forgejo and served by any static host. Do not assume GitHub Pages is enabled until checked in repository settings.
