# Dev log

Newest first. **Claude:** read this at the start of a session for recent context, and add an entry here after making changes.

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
