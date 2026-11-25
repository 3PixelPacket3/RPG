# AGENTS.md — Project Engineering Invariants

## Architecture & Stack Rules
- **Frontend:** Vite + Vanilla ES6+ JavaScript, HTML5, and modular CSS3 (zero heavy framework bloat like React/Angular). Build as a mobile-first SPA/PWA that expands into a multi-column Desktop Command Center at 1024px and wider.
- **Mobile Packaging:** Capacitor 8 targeting Android (Google Play) and iOS (Apple App Store) using `@capacitor-firebase/*` plugins and native safe-area insets.
- **Backend:** Firebase (Auth, Cloud Firestore, Cloud Functions v2 on Node.js 22+, Cloud Scheduler, Cloud Messaging, App Check, Hosting).
- **Server-Authoritative Rule (CRITICAL):** Never calculate skill rolls, payouts, stock trades, bank transfers, laundering, or NPC ticks on the client. All game state mutations MUST occur inside Firebase Cloud Functions using atomic Firestore transactions (`runTransaction`) and idempotency keys. Client Firestore rules MUST be read-only for all economy, stat, inventory, and NPC collections.

## Code Quality & Testing
- Keep shared formulas (skill roll math, career gates, class trees, item definitions, economy constants) in `/shared` so both `/web` (for UI previews/estimates) and `/functions` (for authoritative execution) import the exact same source of truth.
- Every new Cloud Function or game mechanic MUST include Vitest unit tests and Firebase Emulator integration tests.
- After every phase, run `npm test` and `npm run build`, then verify the rendered UI in both mobile (390x844) and desktop (1440x900) viewports via `/browser` with zero console errors.

## Design & UX Standards
- Dark-first "Noir Financial Terminal" palette (deep slate/obsidian surfaces, emerald for clean gains, crimson for underworld/risk, amber for Audit Heat/warnings) with a working Light Mode toggle.
- Mobile views must avoid horizontal overflow and respect `env(safe-area-inset-top)` and `env(safe-area-inset-bottom)`.
- Never hardcode secrets, API keys (other than public Firebase web config), or admin UIDs in source files; use environment variables and Firebase custom claims.
