# Dev log

Newest first. **Claude:** read this at the start of a session for recent context, and add an entry here after making changes.

## 2026-06-26 — Learn SQL · Multiple Tables module (+ Further deck + 10 JOIN/UNION/CTE tasks)
Fourth Learn SQL module ([cheatsheet](https://www.codecademy.com/learn/paths/bi-data-analyst/tracks/dsf-learn-sql/modules/dsinf-learn-sql-multiple-tables/cheatsheet)) — joins, UNION, CTEs, primary/foreign keys — built to mirror the Aggregate Functions module (theory + a "Claude's Further Studies" swap deck + a practical task group).

**Theory — module `sql-multiple-tables` (group "Learn SQL", name "Multiple Tables"):** 8 base cards (3 variants each) tracking the cheatsheet — INNER JOIN, the ON clause, LEFT JOIN, CROSS JOIN, UNION, WITH/CTE, primary key, foreign key.

**"Claude's Further Studies" (second module to use it):** a 9-card `further` swap deck aimed at *comprehending* joins, per the user's ask — INNER vs LEFT (intersection / dropped rows), the LEFT-JOIN **anti-join** (`WHERE right.key IS NULL`), **RIGHT / FULL OUTER JOIN** conceptually (+ the SQLite "swap the tables" note), CROSS JOIN as a Cartesian product and "inner = cross + filter", **qualifying columns / table aliases** for ambiguous names, **UNION vs UNION ALL** (dedupe vs keep-all + speed), **JOIN (horizontal) vs UNION (vertical)**, CTEs vs nested subqueries (+ chaining), and the **PK↔FK / one-to-many** relationship and what you actually join ON. Same module id, distinct `explain` per card → 17 unique cardIds, mastery tracks for free; `further` excluded from the headline Cards total (72→80, i.e. +8 base only). The picker shows the ✦ Further toggle beside the module (8 ⇄ 9 swap). Also de-hardcoded the shared `fbtn.title` tooltip (it used to name "GROUP BY & HAVING").

**Practical — new group `MT` ("SQL Practice · Multiple Tables"), 10 tasks** on two new setups: a `LIBRARY` (authors / books with `author_id` FK→`authors.id`, + a 2-row `formats` table) and `SUBSCRIBERS` (newspaper / online lists). Data tuned for deterministic single-answer grading: **Orwell (id 3) has no books** so LEFT JOIN / anti-join / LEFT-JOIN-COUNT are deterministic (only Orwell unmatched, COUNT 0); the two subscriber lists overlap on Lewis & Ramona so **UNION → 4 distinct rows vs UNION ALL → 6** is a clean contrast. Tasks: INNER JOIN, JOIN+WHERE, LEFT JOIN, anti-join, CROSS JOIN (4×2=8), UNION, UNION ALL, WITH/CTE (join inside a CTE then filter), JOIN+GROUP BY COUNT (combines the previous module), and table aliases. All SELECT tasks (`verify: null`), all unordered (result-set compare sorts), so equivalent queries pass.

**Verified in-browser** (preview on :8123): script parses, **no console errors**; picker shows "Multiple Tables — 8 cards" with a ✦ Further toggle under Learn SQL; toggle on → "9 cards" + auto-selects, off → back to 8; Cards total 72→80, SQL tasks 36→46; Practical lists "Multiple Tables — 10 tasks"; 17 Multiple-Tables cardIds all unique. **All 10 reference solutions grade correct** via the real `gradeTask`/sql.js; equivalent alternatives pass (anti-join via `NOT IN`, inner join via implicit comma-join); a deliberately-wrong UNION (using UNION ALL where dedupe is expected) correctly fails.

## 2026-06-23 — "Claude's Further Studies": per-module toggle that *swaps* the deck
Reworked the feature shipped earlier today (see next entry) per user feedback: instead of a single global *Options* toggle that **added** every module's bonus cards on top of the basics, the toggle now lives **beside the module it belongs to** and **swaps** that module's deck — toggle on = study *only* the deeper recommended cards; off = the basic set as usual.

- **Removed** the global `#furtherToggle` from the *Options* section.
- **Picker:** modules with a `further` array now render inside a `.module-row` flex wrapper — the selectable `.choice.sub` button plus a compact `.further-toggle` ("✦ Further") button beside it. State is the in-memory `furtherModules` Set (default off, like `collapsedGroups`). Clicking it: flips membership, **auto-selects the module** (so "study the further set" always has an effect), updates the `aria-pressed`/`.on` state, and rewrites the count badge to the active deck size (9 ⇄ 8). The "on" state uses the accent gradient + `#f8f3e8` text, matching the primary buttons.
- **`buildQuestions`:** for each selected module, `deck = furtherModules.has(id) && m.further ? m.further : m.questions` — a clean swap, no concatenation. (Old additive `includeFurther` logic deleted.)
- Mastery still tracks via stable `cardId`s; `further` cards still excluded from the headline totals.

**Verified in-browser:** no console errors; exactly one "✦ Further" toggle, beside Aggregate Functions; OFF → 9 cards (basics present), ON → badge "8 cards" and `buildQuestions` returns the 8 *further* cards with **no basic cards** (true swap), toggle gains `.on`; toggling back restores 9. Responsive at 375px — module name wraps, toggle sits beside it, no horizontal overflow.

## 2026-06-23 — Learn SQL · Aggregate Functions module + "Claude's Further Studies" toggle
Third Learn SQL module ([cheatsheet](https://www.codecademy.com/learn/paths/bi-data-analyst/tracks/dsf-learn-sql/modules/dsinf-learn-sql-aggregate-functions/cheatsheet)), plus a new opt-in bonus-deck mechanism the user asked for.

**Theory — module `sql-aggregates` (group "Learn SQL", name "Aggregate Functions"):** 9 base cards (3 variants each) tracking the cheatsheet — the aggregate concept, COUNT, SUM, MAX, MIN, AVG, ROUND, GROUP BY, HAVING.

**"Claude's Further Studies" (new feature):** a module can now carry a `further: [...]` array — same card shape as `questions`, an opt-in bonus deck. A new **toggle** in the start-screen *Options* (`#furtherToggle`, off by default, label "✦ Claude's Further Studies / Bonus deep-dive cards on the tricky bits — GROUP BY & HAVING") controls it. `buildQuestions` appends each selected module's `further` cards **only when the toggle is on**. The Aggregate module ships 8 further cards aimed at the parts the user flagged as hard: WHERE-vs-HAVING, clause evaluation order (FROM→WHERE→GROUP BY→HAVING→SELECT→ORDER BY), COUNT(*) vs COUNT(col) on NULLs, aggregates ignoring NULL, the bare-non-grouped-column rule, multi-column GROUP BY, GROUP BY/ORDER BY by position, and a layered GROUP BY+HAVING+ORDER BY read. They live on the **same module id**, so their `cardId` (= moduleId + hash(explain)) is stable and mastery tracks for free; distinct `explain` text means no id collisions with the base cards (verified: 17 unique ids). Bonus cards are **excluded from the headline "Cards" mastery total** (allCards/masteryStats still iterate only `m.questions`) — they're extra, not part of the cheatsheet count. The module picker badge advertises them: "9 cards **+8 bonus**".

**Practical — new group `AGG` ("SQL Practice · Aggregates"), 10 tasks** on a new `APPS` theme (an app store: name, category, downloads, price). Data is tuned for deterministic single-answer grading: downloads are all unique (MAX/MIN/ORDER BY deterministic) and total 70000 over 7 rows so AVG is a clean 10000; categories Education×2/Lifestyle×2/Music×2/Food×1 with all-distinct group sums (so the top-category task has one answer — Music, 35000). Tasks: COUNT, SUM, MAX, MIN, AVG, ROUND(AVG(price),2)=1.42, GROUP BY+COUNT, GROUP BY+SUM, HAVING COUNT(*)>1, and a GROUP BY+ORDER BY+LIMIT top-group. All SELECT tasks (`verify: null`); GROUP BY tasks left unordered so the result-set compare sorts rows; the top-group task is `ordered: true`.

**Verified in-browser** (preview on :8123): script parses, no console errors/warnings; picker shows "Aggregate Functions — 9 cards +8 bonus" under Learn SQL; Cards total 63→72, SQL tasks 26→36; Practical tab lists "Aggregates — 10 tasks". Toggle off → 9 aggregate cards in the build, on → 17 (9+8); all 17 aggregate card ids unique. **All 10 reference solutions grade correct**; an equivalent HAVING alt (`>= 2`) passes; a deliberately-wrong COUNT fails.

## 2026-06-23 — Removed "due" scheduling; fixed shuffle feeling broken
Two user complaints, one root cause. (1) "Randomise questions doesn't work" and (2) "remove all the 'due' stuff — I dislike being told I have work due, especially as this grows; doing them all isn't feasible." Both traced to the **Leitner due-date scheduling**: the default "Smart review" mode filtered each session down to only *due* cards, and once correct answers scheduled cards into the future it just resurfaced the same "soonest few". So **Shuffle question order** was working — it was only ever reordering that small repeating subset, which read as "not random".

**Fix — ripped out due-date scheduling entirely, kept mastery:**
- Dropped the **Smart review** mode. Study modes are now just **All cards** (new default — the whole selected bank, shuffled) and **Weak spots**. `buildQuestions` no longer filters/sorts by due; with "All cards" + shuffle on, every session is a fresh shuffle of all selected cards (verified: two builds both cover all 63 cards, 62/63 positions differ).
- Removed `isDue`, `dueCount`, `BOX_DAYS`/`DAY`, and the `due` field. `recordResult` keeps the **box** (right → promote up to 5, wrong → box 1) purely as a mastery signal — it still powers the **Mastered** stat (box ≥ 4) and **Weak spots** (`seen && box ≤ 2`). Old exports carrying a stale `due` import fine (ignored).
- Stripped every "N due" surface: the **Due now** progress stat, the per-module and per-practice-set "· N due" badges, and the tab subtitles (`updateTabBadges` deleted — subtitles are static "flashcards" / "write SQL" again). `buildTasks` now returns the selected SQL sets weakest-first by box, no due gate.
- Reworded the empty-session note (was "Nothing due…"), the start-button label ("Study all cards →"), and the in-code comments (no more "spaced-repetition schedule" / "Leitner SRS"). Updated `CLAUDE.md` to describe mastery boxes without scheduling.

Verified in the preview: no console errors; start screen shows Mastered/Cards/SQL Tasks/Best Quiz (no "Due now"); modes are All cards + Weak spots; module/tab badges are plain counts; weak mode returns only weak cards while All cards returns the full bank; `recordResult` writes no `due` field.

## 2026-06-22 — Dark mode (moon/sun toggle, top right)
Added a dark theme keeping the same warm, parchment aesthetic — just deeper espresso tones for easier night reading. Implementation is a near-pure palette swap because nearly everything already used CSS variables: a `:root[data-theme="dark"]` block overrides the colour vars (deep browns for `--bg`/`--card`, the old light parchment `#ece3d0` becomes `--text`, accent brightened to `#c2945c` so links/borders read on dark, green/red nudged brighter). The few hardcoded `#f8f3e8` button-text colours sit on the accent/green/red gradients, which stay rich enough that light text still passes — no per-element dark overrides needed.

A round moon/sun button (`#themeToggle`, inline SVGs) lives in a new `.head-actions` wrapper next to the Best badge in the header; CSS shows the moon in light mode, sun in dark. Theme is a **UI preference**, so it's stored under its own `biStudyTheme` key (NOT the synced `biStudyData` blob — it shouldn't travel between devices via export/import). Applied **pre-paint** by a tiny inline `<head>` script (reads localStorage, else `prefers-color-scheme`) to avoid a flash of the wrong mode; the main `initTheme()` IIFE wires the click, persists, and keeps `<meta name="theme-color">` + the button's `aria-label` in sync. `color-scheme` is set per theme so form controls/scrollbars match. Verified both modes + toggle/persistence in the preview.

## 2026-06-22 — SQL Practice: write & run real SQL (sql.js)
New "separate part" the user asked for: instead of only recognising SQL in multiple-choice cards, you now **write real queries** and they run and auto-check against a sample database. Reached from a **Theory / Practical segmented tab** on the start screen (replaced the original long-scroll where SQL Practice was a section at the bottom). Shared chrome (progress summary + export/import) stays above the tabs; each tab shows a live "N due" badge. `activeTab` follows the session — finishing/quitting a practice run lands you back on Practical, a quiz on Theory. State: `activeTab` + `applyTab()`/`selectTab()`/`updateTabBadges()`; the two panels (`#theoryTab`, `#practicalTab`) just toggle `.hidden`.

**Constraint change:** the user explicitly relaxed the old "everything in one `index.html` / no dependencies" rule — the real bar is just *works on GitHub Pages + works on iPhone*. That unlocked real execution. Updated `CLAUDE.md` to match.

**Engine:** vendored **sql.js** (SQLite → WASM, v1.10.3) into `vendor/sql-wasm.js` + `vendor/sql-wasm.wasm` (committed, served same-origin, **not** a third-party CDN). `ensureSql()` lazy-loads it (cached promise) only when Practice first opens, so the flashcard app is unaffected and the ~640 KB WASM never loads otherwise.

**Tasks:** `SQL_TASKS` (after `MODULES`) — 26 tasks across two groups, mirroring the existing Learn SQL modules. Manipulation (`celebs` theme, 12): CREATE/INSERT/SELECT/SELECT */ALTER/UPDATE/DELETE/DELETE-all/PRIMARY KEY/UNIQUE/NOT NULL/DEFAULT. Queries (`movies` theme, 14): specific columns, AS, DISTINCT, WHERE, LIKE + `%` + `_`, BETWEEN, AND, OR, IS NULL, ORDER BY, DESC, LIMIT, CASE.

**Grading** compares **result sets, not text** (`gradeTask`→`sameResult`), so any equivalent query passes (verified: `year >= 2011` passes a `year > 2010` task). SELECT tasks compare query output; **mutation** tasks set `verify` (a SELECT run after the user's statements) and compare resulting DB *state*. Fresh in-memory DB per attempt — no accumulation. Flags: `ordered` (row order matters: ORDER BY/LIMIT), `checkColumns` (column names matter: AS/CASE). CREATE verifies use `UPPER(type)` so lowercase `integer`/`text` pass. Progress feeds the same Leitner SRS under `sqlt-`-prefixed ids (syncs via export/import for free); added a "SQL tasks" stat tile. "Mark correct anyway" is a safety valve for valid alternatives the checker misses.

**Decisions:** kept it in `index.html` (not a separate page) to reuse the SRS, export/import, styling, and screen-switching — only the engine is external. Sample `imdb_rating`/`year` values are deliberately unique so ORDER BY/LIMIT have a single deterministic answer (otherwise ties break ordered comparison). First attempt counts toward SRS.

**Verified in-browser** (localhost via preview tools): engine lazy-loads from `vendor/` with no console errors; all 26 reference solutions grade correct; deliberately-wrong queries all fail; SQL syntax errors surface; mutation grading checks final state and a re-attempt starts clean; `ordered`/`checkColumns` enforce; SRS record written under `sqlt-…`; responsive at iPhone width (375px) with the SQL field at 16px to stop iOS zoom and wide tables scrolling horizontally.

**Follow-up (not built):** no service worker, so true offline on the Home Screen relies on the browser HTTP cache (same as today). If guaranteed offline is wanted later, add a small SW to cache `index.html` + `vendor/*`.

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
