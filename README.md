# OpenOps Platform — Engineering Handbook

Technical design, research, decision records, and planned work for the collaborative [OpenOps Platform](https://github.com/ctalaveraw/openops-platform) engineering project.

This repository documents the architecture and its tradeoffs. It is not the runtime configuration or implementation source of truth. Planned systems are not represented as deployed systems.

## Read the handbook

- [Handbook home and contents](docs/index.md)
- [Project charter](docs/Project-Charter.md)
- [Architecture and ownership](docs/Platform-Architecture.md)
- [Decision register](docs/Decision-Register.md)
- [Roadmap and acceptance gates](docs/Roadmap-and-Acceptance.md)
- [Engineering governance](docs/Engineering-Governance.md)
- [Maintenance standard](docs/Handbook-Maintenance.md)

The full article list is available in [the handbook index](docs/index.md).

## Repository boundaries

| Repository | Authority |
| --- | --- |
| [openops-platform](https://github.com/ctalaveraw/openops-platform) | Implementation, executable configuration, tests, infrastructure code, change review, and deployment evidence |
| **openops-platform-handbook** | Current architectural planning, evaluated alternatives, research, and proposed work |

Accepted implementation decisions should be promoted to reviewed ADRs in the platform repository. Issues and pull requests are the record of work performed. The handbook records *why* architectural choices are being considered.

## Preview locally

The articles are ordinary Markdown and can be read directly on GitHub. A Python-based site preview is optional:

~~~sh
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -r requirements-docs.txt
mkdocs serve
~~~

A built static site can be produced with `mkdocs build --strict`. Hosting is deliberately independent of the document source and has not yet been configured.

## Contributions

Treat pages as maintained technical documents: revise the whole affected section, reconcile superseded decisions, and use clear implementation-status labels. Do not append conversation transcripts or create parallel, contradictory histories. See the [maintenance standard](docs/Handbook-Maintenance.md).
