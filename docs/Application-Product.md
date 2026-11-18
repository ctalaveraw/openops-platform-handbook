# Application Product — Anime Catalog and Watchlist

**Status:** Candidate initial product; not implemented in the target repository

**Reviewed:** 2026-11-17

## Product premise

An anime catalog provides enough real interaction to exercise development, testing and delivery while remaining independent of infrastructure. The first release should be useful without an account, remote database, API token or server-side business logic.

## MVP behavior

- Display a small, locally bundled catalog from versioned JSON data.
- Search titles and filter using a short set of metadata fields.
- Add or remove entries from a personal watchlist.
- Mark entries planned, watching or completed.
- Preserve personal selections using browser storage.
- Show useful error/empty states when catalog data is unavailable or malformed.
- Provide accessible controls, visible focus states and a responsive layout.

The visual design can be developed independently of the delivery platform. Alex may participate in frontend design, but a working basic UI should not depend on availability of a specialized frontend engineer.

## Interfaces and data contracts

Initial delivery exposes static HTTP assets (`/`, `/app.js`, `/style.css`, `/data/anime.json`). Fetch a versioned JSON schema from the same origin. Keep schema migration and sample fixtures in the source repository. The browser may use `localStorage` for preference state. Never store secrets or privileged credentials in client-side state.

A static file served at `/data/anime.json` is a read-only HTTP resource, not a dynamic authenticated API. External API integration is a later option subject to CORS, rate limits, data rights and browser-exposed credentials.

## Constraints and limitations

Browser storage is device-local and may be deleted by the user or browser. It is not cross-device account synchronization or an authoritative user database. A browser-only application cannot safely hide an API key. Client-side JavaScript errors appear primarily in browser tooling, not in container stdout. Automated browser tests and optional client error reporting are separate concerns.

## Future features, not part of the MVP

Import/export of a watchlist; sorting and richer filters; multiple themes; recommendation logic; metadata updates from a public API; optional account synchronization; multiplayer/social functionality. A shared stateful API would be a separately deployable service with its own authentication, storage, data migrations and reliability obligations.

## Acceptance tests

Test search and filtering; watchlist persistence across reloads; invalid/missing data; a clean browser console; accessibility smoke checks; deterministic fixtures; same behavior from local static hosting and OCI container. Use Python/pytest plus Playwright Python where appropriate.

## Work breakdown

Keep early issues small: baseline catalog, data-schema validation, one filter, watchlist state, browser test, container contract. Avoid introducing a framework solely to achieve visual polish.
