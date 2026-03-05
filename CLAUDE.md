# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Stack

Laravel 12 + React 19 (Inertia.js) starter kit. PHP 8.2+, TypeScript, Tailwind CSS v4, Vite. Authentication via Laravel Fortify. UI primitives via Radix UI (shadcn-style components in `resources/js/components/ui/`).

## Commands

### Development
```bash
composer run dev          # Start PHP server, queue listener, and Vite concurrently
composer run dev:ssr      # Start with SSR (builds first, then starts SSR server)
```

### Build
```bash
npm run build             # Vite build
npm run build:ssr         # Vite build + SSR build
```

### Linting & Formatting
```bash
composer run lint         # PHP: run Pint (auto-fix)
composer run lint:check   # PHP: run Pint (check only, no changes)
npm run lint              # JS/TS: ESLint (auto-fix)
npm run lint:check        # JS/TS: ESLint (check only)
npm run format            # Prettier (auto-fix, resources/ only)
npm run format:check      # Prettier (check only)
npm run types:check       # TypeScript type check (no emit)
```

### Testing
```bash
php artisan test                          # Run all tests
php artisan test --filter TestClassName   # Run a single test class
php artisan test tests/Feature/Foo.php    # Run a specific file
composer run test                         # Clears config cache, checks PHP lint, then runs tests
composer run ci:check                     # Full CI: JS lint, format check, type check, PHP test
```

Tests use SQLite in-memory (`DB_CONNECTION=sqlite`, `DB_DATABASE=:memory:`).

### Setup (first time)
```bash
composer run setup        # install deps, copy .env, generate key, migrate, npm install & build
```

## Architecture

### Backend (`app/`)
- `Http/Controllers/` — standard Laravel controllers; `Settings/` subdirectory handles profile, password, and 2FA
- `Actions/Fortify/` — Fortify action classes (`CreateNewUser`, `ResetUserPassword`)
- `Concerns/` — PHP traits (`PasswordValidationRules`, `ProfileValidationRules`) shared between actions and controllers
- `Models/User.php` — single Eloquent model; authentication model for Fortify

### Routing (`routes/`)
- `web.php` — public routes and authenticated dashboard; includes `settings.php`
- `settings.php` — settings routes (profile, password, appearance, 2FA)

### Frontend (`resources/js/`)
- `pages/` — Inertia page components, resolved by route name (e.g. route `dashboard` → `pages/dashboard.tsx`)
- `components/` — shared React components; `ui/` contains Radix-based primitives
- `layouts/` — layout wrappers used by pages
- `hooks/` — custom React hooks
- `lib/` — utility functions (e.g. `cn()` for class merging)
- `types/` — TypeScript type definitions
- `wayfinder/` — **auto-generated** type-safe route helpers from Laravel Wayfinder; do not edit manually

### Key conventions
- Inertia pages are resolved from `resources/js/pages/` automatically; the file path must match the route name passed to `Route::inertia()` or returned from a controller.
- Laravel Wayfinder generates `resources/js/wayfinder/` on build; use these helpers for type-safe route references in React instead of hardcoding URLs.
- Tailwind CSS v4 — configured via CSS (`resources/css/app.css`), not a `tailwind.config.js` file.
- React Compiler (`babel-plugin-react-compiler`) is enabled; avoid manual `useMemo`/`useCallback` for pure memoization.
- PHP code style follows the Laravel preset (enforced by Pint).
