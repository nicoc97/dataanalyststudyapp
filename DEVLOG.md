# Dev log

Newest first. **Claude:** read this at the start of a session for recent context, and add an entry here after making changes.

## 2026-06-22 — Added "Learn SQL · Queries" module
Second module of the Learn SQL track ([cheatsheet](https://www.codecademy.com/learn/paths/bi-data-analyst/tracks/dsf-learn-sql/modules/dsinf-learn-sql-queries/cheatsheet)).

**Added:** module `id: "sql-queries"`, `group: "Learn SQL"`, name "Queries" — 15 cards (3 variants each) covering SELECT (specific columns), AS (aliases), DISTINCT, WHERE (+ comparison operators), LIKE, the `%` and `_` wildcards, BETWEEN (inclusive range), AND, OR, IS NULL / IS NOT NULL, ORDER BY (+ default ASC), DESC, LIMIT, and CASE.

**Note:** the WebFetch summary of the cheatsheet didn't surface CASE, but CASE is the signature topic of the Codecademy Queries module, so it's included. Flagged to the user to confirm against their cheatsheet version.

**Verified in-browser:** no console errors; "Learn SQL" group now lists Manipulation + Queries (15 cards each); total deck 48 → 63 cards.

## 2026-06-22 — Accordion module groups + dropped "Section" prefix
**Accordion:** each module-group heading is now a clickable button (`.group-label`) with a rotating chevron that expands/collapses its sub-modules. Body open/close animates via `grid-template-rows: 1fr ⇄ 0fr` on `.group-body` with an `overflow:hidden` `.group-body-inner` wrapper (the modern fr-row trick — no JS height math, smooth on iOS Safari 16+). Open/closed state lives in an in-memory `collapsedGroups` Set (default: all open; resets on reload — it's view state, not study data). Toggling only flips classes/aria, so it never touches `selectedModules` — a collapsed group's modules stay selected.
- Gotcha noted: spacing between heading and first card lives on the heading's `padding-bottom`, **not** the inner wrapper. Padding on the inner doesn't clip with the fr-row collapse, so it left an ~8px sliver when closed. Verified collapsed body settles to exactly 0 (headers 10px apart = just the grid gap).

**Renamed** the two intro modules to drop the word "Section": now `1 · Path Overview & Intro to Data` and `2 · Introduction to Data` (the group heading already says it's the career-path intro).

**Verified in-browser:** no console errors; chevron rotates; collapsing hides sub-modules and the body height goes to 0; expanding restores them; names render without "Section".

## 2026-06-22 — Grouped module picker + removed day streak
**Grouped modules:** added a `group` field to each module object (kept `MODULES` flat so all card/selection logic is unchanged) and reworked `renderModules()` to render a group heading with its sub-modules indented beneath. Groups now: **Welcome to the BI Data Analyst Career Path** (Section 1, Section 2) and **Learn SQL** (Manipulation). Renamed the SQL module from "Learn SQL · Manipulation" to just "Manipulation" since the group header carries the track name. New `.group-label` style + `.choice.sub` indent (`width:auto` + `margin-left` so the grid stretches the button to cell-minus-indent, no overflow).

**Removed day streak** entirely (user request): dropped the stat tile, `touchStreak()`/`dayStr()` and the call in `recordResult`, the `streak` init in `loadData`, and the streak branch in `mergeData`. Old `streak` data left in any existing localStorage/export blob is simply ignored — harmless. Stats row is now 4 tiles (Mastered, Cards, Due now, Best quiz) and the flex layout reflows fine.

**Verified in-browser:** no console errors; picker renders both groups with correct sub-modules and `N cards · N due` badges; sub-items indented 14px with no container overflow; stats row no longer shows Day streak.

## 2026-06-22 — Added "Learn SQL · Manipulation" module
First module of the new **Learn SQL** track ([cheatsheet](https://www.codecademy.com/learn/paths/bi-data-analyst/tracks/dsf-learn-sql/modules/dsinf-learn-sql-manipulation/cheatsheet)).

**Added:** module `id: "sql-manipulation"`, 15 cards (3 variants each), covering CREATE TABLE (purpose + parentheses syntax), INSERT INTO (purpose + naming columns), SELECT (purpose + `*` wildcard), ALTER TABLE … ADD COLUMN, UPDATE (purpose + SET/WHERE roles), DELETE (purpose + the no-WHERE "deletes all rows" gotcha), and the four column constraints (PRIMARY KEY, UNIQUE, NOT NULL, DEFAULT).

**Naming:** broke from the `Section N ·` convention since this starts a new track — named it `Learn SQL · Manipulation` so the track is obvious in the module picker.

**Verified in-browser:** script parses; no console errors; picker shows "Learn SQL · Manipulation — 15 cards · 15 due"; total deck 33 → 48 cards.

## 2026-06-21 — Spaced repetition, progress tracking, export/import sync
Commit `185e392`.

**Added:**
- Leitner-box spaced repetition. Each card has a box 1–5; a right answer promotes it (intervals 1 / 2 / 4 / 8 / 16 days), a wrong answer resets it to box 1 and re-shows it next session.
- Card identity = `moduleId + hash(explain)`, so progress survives rewording or adding variants.
- Three study modes on the start screen: **Smart review** (due + new cards, weakest first), **All cards**, **Weak spots** (cards repeatedly missed).
- Progress stats row: mastery %, total cards, due-now count, day streak, best quiz. Per-module "N due" badges.
- Manual cross-device sync: **Export** copies all progress to clipboard as JSON; **Import** pastes + merges (per card, the most-recent `lastSeen` wins; best score and streak take the higher value).
- All state consolidated under `localStorage["biStudyData"]`; the old `biStudyBest` key migrates automatically.

**Why manual sync:** GitHub Pages is static — automatic sync would need a server or a secret embedded in this public repo, both unsafe for a personal app.

**Verified in-browser:** no console errors; SRS records write on answer; box promotion `[1,2]` then back to `1` on a wrong answer; streak starts at 1; import merge keeps newer card records and the higher best score.

**Possible next steps (brainstormed, not built):**
- Star/flag tricky cards for a focused review set.
- Add "why the other answers are wrong" to `explain`.
- More cloze (fill-in-the-blank) cards.
- A "reset all progress" link next to export/import.

## (earlier) — Initial app
Commit `108a409`. Responsive multiple-choice flashcards: 3 wordings per card, module picker, shuffle toggles, missed-question review, best-score badge.
