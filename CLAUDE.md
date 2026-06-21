# CLAUDE.md

Guidance for Claude working in this repo. **Read this first, then skim [`DEVLOG.md`](DEVLOG.md) for recent history before making changes.**

## What this is
A single-file study app — multiple-choice flashcards for the Codecademy **BI Data Analyst** career path. The user adds a new module as they finish each Codecademy section.

## Hard constraints (do not break)
- **Everything lives in `index.html`** — HTML + CSS + vanilla JS. No build step, no dependencies, no framework. It must stay one self-contained file.
- **Hosted on GitHub Pages** (deploys from `main`) and opened mostly on **iPhone** (added to Home Screen). Keep it responsive / mobile-first and respect safe-area insets.
- **No backend, no database.** Persistence is `localStorage` only, via the `store` try/catch helper. Cross-device sync is manual export/import of a JSON blob. Never add a server, a build pipeline, or embed API secrets in this public repo.

## How it works
- `MODULES` (top of the `<script>`) is the question bank: `{ id, name, questions: [...] }`.
- Each card: `{ variants: [ { q, options:[...], answer:<index> }, ... ], explain }`. One variant is picked at random per session (varied retrieval). Legacy `{ q, options, answer, explain }` cards still work.
- **Spaced repetition (Leitner):** progress is keyed by a card's *concept*, not its wording — `cardId = moduleId + hash(explain)`. Rewording or adding variants keeps a card's history. Boxes 1–5, intervals `BOX_DAYS = [_,1,2,4,8,16]` days; a right answer promotes a box, a wrong answer drops to box 1.
- All persistent state is one object under the `biStudyData` localStorage key: `{ version, best, streak, srs }`.

## Adding a module
Append an object to `MODULES`. Easiest path: the user pastes a Codecademy cheatsheet and Claude generates the module — 3 variants per card plus an `explain` — matching the existing wording and style.

## Keep the devlog
After making changes in a session, add a dated entry to the **top** of [`DEVLOG.md`](DEVLOG.md) (newest first): what changed, why, key decisions, and any follow-ups worth remembering next time.
