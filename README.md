# Refactorizando — devlog008

A 1:1 migration of **Refactorizando**, a legacy **WinDev** (PCSoft) business application, rebuilt from the ground up as a modern web app with **SvelteKit 2 + Svelte 5 + SQLite**. The original WLanguage code and HFSQL schema were translated piece by piece — table definitions, validation rules, and even the classic-vs-lambda teaching demo — while keeping behavioral parity with the desktop original. This repository is the companion of the devlog008 live coding series.

## Tech Stack

- [SvelteKit 2](https://kit.svelte.dev/) — routing, API endpoints, SSR
- [Svelte 5](https://svelte.dev/) — runes (`$state`, `$effect`, `$derived`), transitions
- [better-sqlite3](https://github.com/WiseLibs/better-sqlite3) — embedded local database
- [Vitest](https://vitest.dev/) — unit and API route tests
- [TypeScript](https://www.typescriptlang.org/) — end-to-end type safety

## What the App Does

### Products CRUD (`/products`)

Create/read/update of products backed by a local SQLite database (`app/data/refactorizando.db`), served through REST endpoints (`/api/product`, `/api/product/[id]`) migrated from the original WinDev WEBDEV webservice (`Mproduct` class). The validation layer mirrors the legacy HFSQL column rules exactly (see [WinDev parity](#development-notes--windev-parity)).

### Users demo (`/users`)

Migrated from the `WIN_JSONS` window: it fetches users from [dummyjson.com](https://dummyjson.com/users) and demonstrates **Map / Filter / Reduce / Combine** — each operator with both a **classic** (named function) and a **lambda** implementation, side by side, with staggered output animation. It is the JS counterpart of the original WLanguage exercise.

## Getting Started

Prerequisites: **Node.js** (18+ recommended — required by SvelteKit 2 and better-sqlite3).

```bash
cd app
npm install

# Development server
npm run dev

# Run the test suite (34 tests)
npm test

# Type-check / Svelte diagnostics
npm run check

# Production build
npm run build
```

The SQLite database is created and seeded automatically on first run under `app/data/` — no manual setup needed.

## Project Structure

```
app/
├── data/                      # SQLite database (auto-created)
├── src/
│   ├── routes/
│   │   ├── +page.svelte           # Home: beard hero + section tiles
│   │   ├── products/              # Products CRUD pages (list, new, edit)
│   │   ├── users/                 # Map/Filter/Reduce demo over dummyjson
│   │   └── api/product/           # REST endpoints (GET, POST, [id] GET/PUT)
│   ├── lib/
│   │   ├── server/                # better-sqlite3 connection + product validation
│   │   ├── components/ui/         # Design system: Card, Button, Table, Badge, Toast
│   │   ├── components/            # BeardHero, ProductForm
│   │   ├── stores/                # Toast store
│   │   ├── utils/                 # Formatting helpers
│   │   └── assets/                # Braille beard art (TS constants)
│   └── app.css                    # Design tokens + IBM Plex Mono / Inter fonts
└── tests/                     # Test setup
```

## Design System

The UI follows a dark palette inspired by [refactoria.dev](https://refactoria.dev): deep-purple/near-black surfaces (`--dark-900: #0b0714`) with a violet brand accent (`--brand-500: #7127ff`). The home hero renders the signature **Braille beard mark** as animated text art (row-by-row reveal, `prefers-reduced-motion` aware), and the monospace stack uses **IBM Plex Mono**. All primitives live in `app/src/lib/components/ui/`.

## Development Notes — WinDev Parity

The migration is deliberately faithful to the legacy HFSQL analysis:

- **5 migrated tables**: `entity`, `entityPhones`, `product`, `product_2023`, `registros` (including the `WDIDX_entityPhones_IDentity` index and FK to `entity`).
- **Validation parity oracle**: the product schema was `name VARCHAR(50)` and `price NUMERIC(24,6)` — so the modern validation layer rejects names over 50 characters and prices with more than 6 decimal places, exactly like the original did.
- API responses keep the legacy casing conventions (`IDproduct`, `isActive`).

## Testing

The suite runs **34 tests** with Vitest:

- **Validation** (`product-validation.test.ts`) — 16 tests covering name/price rules, including the VARCHAR(50) and NUMERIC(24,6) parity cases.
- **API routes** (`product-routes.test.ts`) — 12 tests against a fresh **in-memory SQLite** database per test (via `vi.doMock('$lib/server/db')`), covering GET/POST/[id] handlers.
- **Formatting** (`format.test.ts`) — 6 tests for currency/number helpers.

Run them with:

```bash
cd app && npm test
```

## License

[MIT](LICENSE) © refactor-ia

## Credits

- The **Braille beard mark** and the dark design palette are inspired by [refactoria.dev](https://refactoria.dev), used with permission.
- Legacy WinDev reference material © its original owner; only the migrated, original code is distributed here.
---

<a href="https://github.com/Gentleman-Programming/gentle-ai">
  <img width="220" src="https://raw.githubusercontent.com/Gentleman-Programming/gentle-ai/main/docs/assets/brand/built-with-gentle-ai.png" alt="Built with Gentle-AI" />
</a>
