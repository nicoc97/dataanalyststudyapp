# CLAUDE.md

Guidance for Claude working in this repo. **Read this first, then skim [`DEVLOG.md`](DEVLOG.md) for recent history before making changes.**

## What this is
A study app for the Codecademy **BI Data Analyst** career path. Two halves:
- **Flashcards** — multiple-choice questions across all modules. The user adds a new module as they finish each Codecademy section.
- **SQL Practice** — write *real* SQL against a sample SQLite database (run in the browser via [sql.js](https://sql.js.org)) and have it auto-checked. Lives in the same app, reached from a section on the start screen.

## Hard constraints (do not break)
- **Must work on GitHub Pages** (deploys from `main`) **and on iPhone** (added to Home Screen). These two are the real bar. Keep it responsive / mobile-first and respect safe-area insets.
- **App stays HTML + CSS + vanilla JS, no build step, no framework.** The app shell is still one `index.html` — but *multiple files are fine* (the original "everything in one file" rule was relaxed by the user). The only third-party dependency is the **vendored** sql.js engine in `vendor/` (`sql-wasm.js` + `sql-wasm.wasm`), served same-origin and lazy-loaded only when SQL Practice opens. Don't pull libraries from third-party CDNs at runtime; vendor them.
- **No backend, no database server.** Persistence is `localStorage` only, via the `store` try/catch helper. Cross-device sync is manual export/import of a JSON blob. Never add a server, a build pipeline, or embed API secrets in this public repo. (The SQLite DBs are ephemeral, in-memory, rebuilt per attempt — not persistence.)

## How it works
- `MODULES` (top of the `<script>`) is the question bank: `{ id, group, name, questions: [...] }`. The picker renders modules grouped under their `group` heading (e.g. "Learn SQL") as collapsible accordions; `MODULES` itself stays a flat array, so all card/selection logic is unchanged.
- Each card: `{ variants: [ { q, options:[...], answer:<index> }, ... ], explain }`. One variant is picked at random per session (varied retrieval). Legacy `{ q, options, answer, explain }` cards still work.
- **Spaced repetition (Leitner):** progress is keyed by a card's *concept*, not its wording — `cardId = moduleId + hash(explain)`. Rewording or adding variants keeps a card's history. Boxes 1–5, intervals `BOX_DAYS = [_,1,2,4,8,16]` days; a right answer promotes a box, a wrong answer drops to box 1.
- All persistent state is one object under the `biStudyData` localStorage key: `{ version, best, srs }`. SQL Practice progress lives in the **same** `srs` map under ids prefixed `sqlt-`, so it syncs via export/import for free.
- **Theming:** light/dark via `:root[data-theme="dark"]` overriding the colour vars — same warm aesthetic, deeper espresso tones. The moon/sun button (`#themeToggle`) in the header flips it. Theme is a UI preference kept in its **own** `biStudyTheme` key (deliberately *not* in `biStudyData`, so it doesn't travel via export/import). Applied pre-paint by an inline `<head>` script (saved value, else `prefers-color-scheme`) to avoid a flash; `initTheme()` wires the toggle and syncs `theme-color` + `color-scheme`. Almost everything themes for free because it already uses the colour vars — add new colours as vars, not hardcoded hex.

## How SQL Practice works
- `SQL_TASKS` (just after `MODULES`) is the task bank, grouped like modules: `{ id, group, name, prompt, tables, setup, solution, verify, ordered?, checkColumns?, hint, explain }`. Sample data lives in shared `setup` constants (`CELEBS`, `MOVIES`, …); ratings/years are kept **unique** so `ORDER BY`/`LIMIT` tasks have one deterministic answer.
- Grading compares **result sets, not query text** (`gradeTask` → `sameResult`), so any equivalent query passes. **SELECT** tasks (`verify: null`) compare the query output against the reference solution's output. **Mutation** tasks (CREATE/INSERT/UPDATE/…) set `verify` (a SELECT run after the user's statements) and compare the resulting DB *state* to the solution's. Each attempt runs against a **fresh in-memory DB** (`runSql`/`runSeq`), so mutations never accumulate.
- Flags: `ordered: true` makes row order matter (ORDER BY/LIMIT); `checkColumns: true` makes result column names matter (AS/alias, CASE … AS label). Comparison is case-sensitive on values, but type checks in CREATE verifies use `UPPER(type)` so lowercase `integer`/`text` still pass.
- Engine: vendored sql.js, lazy-loaded by `ensureSql()` (cached promise) the first time Practice opens. Auto-graded pass/fail feeds `recordResult(task.id, …)` — same Leitner SRS as flashcards. "Mark correct anyway" is the safety valve for a valid alternative the checker didn't anticipate.

## Adding a module
Append an object to `MODULES`. Easiest path: the user pastes a Codecademy cheatsheet and Claude generates the module — 3 variants per card plus an `explain` — matching the existing wording and style.

## Adding a SQL task
Append an object to `SQL_TASKS` with a `sqlt-`-prefixed `id` and the right `group` (`MANIP`/`QUERY` constants). Reuse a shared `setup` constant where possible; write a `solution`, set `verify` for mutation tasks (else `null`), and add `ordered`/`checkColumns` when order or column names matter. Sanity-check by confirming the solution grades correct (the verify step in DEVLOG shows how).

## Keep the devlog
After making changes in a session, add a dated entry to the **top** of [`DEVLOG.md`](DEVLOG.md) (newest first): what changed, why, key decisions, and any follow-ups worth remembering next time.
