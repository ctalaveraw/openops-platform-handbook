# Frontend Architecture and Language Evaluation

**Status:** Decision pending; candidate implementations

**Reviewed:** 2026-11-17

## Design requirements

The runtime must be easy to understand, trace and replace. Rendering and user interaction occur in the browser. No custom backend process is required for the first release. Prefer web platform APIs over framework-specific state machines. Local development should need a static HTTP server rather than Kubernetes, AWS, or a database.

## Options

| Option | Runtime | Benefits | Costs |
| --- | --- | --- | --- |
| HTML/CSS/vanilla JavaScript | Native browser | No runtime framework; direct DevTools debugging; minimal assets | Engineers learn a small amount of JavaScript |
| PyScript with MicroPython | Browser Python interpreter | Python logic in browser; familiar language for the team | Browser interop, startup cost, runtime packaging and debugging constraints |
| PyScript with Pyodide | Full CPython in WebAssembly | Familiar ecosystem and broader Python compatibility | Larger boot/runtime/dependency budget than the product requires |
| FastAPI + HTMX | Python application server | Simple Python endpoints and server-owned behavior | Conflicts with browser-only/no-backend initial requirement |
| React/Vue/Svelte | Browser framework plus build tool | Component abstraction and ecosystem | More tooling, concepts and dependency maintenance than required initially |

## Proposed evaluation experiment

Implement the same small catalog and watchlist feature in vanilla JavaScript and PyScript/MicroPython. Capture source LOC, runtime dependencies, total transferred asset size, cold load time on a representative device, browser console behavior, stack traces, local test burden, container image size and accessibility behavior. Use identical JSON fixtures and acceptance tests. Evaluate maintainability independently from preference for a language.

**Default unless experiments overturn it:** vanilla browser JavaScript for production runtime, Python for validation, fixtures, CLI tooling, Playwright tests and automation. This is a proposal, not an accepted stack decision.

## Runtime and container boundary

Use an ordinary HTTP static server locally. Python's `http.server` is appropriate for development experiments but is not a hardened production web server. Compare Caddy, nginx-unprivileged, and a minimal BusyBox HTTP server on measured size and operational behavior. Prefer an OCI image with static assets and one purpose-built server, non-root execution, no embedded application secrets, a writable-path audit and a verified readiness endpoint.

## Application rules

- Browser code must not depend on cluster names, cloud vendor SDKs, or GitOps configuration.
- Use `fetch`, DOM APIs, localStorage, URL and native browser events directly where they remain understandable.
- No minification/build pipeline is required until evidence warrants one.
- Do not add a server to proxy client requests merely to avoid understanding CORS; document legitimate backend requirements separately.
- Keep input validation both in authoring-time data tests and defensive browser rendering. Prefer safe DOM text APIs over unsanitized `innerHTML`.

## Decision gate

Record an ADR only after both prototypes pass equivalent tests and their tradeoffs are measured. The chosen runtime should survive a fresh-checkout development exercise by both contributors.
