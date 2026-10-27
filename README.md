# Unsolved Case — Season 1 (Web Build)

A free, playable web detective game: six cold cases, one hidden link.
Investigate scenes, interrogate suspects, link evidence on the deduction
board, then accuse — culprit + motive + method + key proof. Score 4/4 to
close the case.

**Play:** https://zakariab2000.github.io/unsolved-case/
(no install; works in any modern browser, phone or desktop)

## The cases

| # | Case | Culprit | Evidence |
|---|------|---------|----------|
| 1 | The Last Ferry | Rosa Venn | 29 |
| 2 | The Bookseller's Last Chapter | Henri Delacroix | 24 |
| 3 | The Final Dress Rehearsal | Inès Marchand | 26 |
| 4 | The Silent Choir | Marcus Hale | 24 |
| 5 | The Glass Tide | Dana Whitfield | 24 |
| 6 | The Anchorman | Corinne Hale | 24 |

Each case: 5 chapters, 4–6 suspects, 3–5 red herrings, mini-puzzles,
interrogations with confrontations, chapter reports, and a finale
accusation. Every case documents **two distinct clue paths** to the
solution in its case bible (`cases/caseN-bible.md`) — no single smoking
gun. The season arc (the black envelopes, the Anchorman) is indexed in
`SEASON1-BIBLE.md`. **Spoilers in the bibles — play first.**

## How to play

1. Pick a case (all six are free and unlocked in this build).
2. Create or join a room to play co-op (2–4 players, PeerJS) or play solo.
3. Tap glowing points in each scene to collect clues, use analysis
   tools (UV, flashlight, fingerprint powder, magnifier) to reveal hidden
   ones, solve the mini-puzzle, interrogate suspects, link evidence on
   the board, answer the chapter report, and accuse in the finale.
4. Stars: 5 max. Hints and report mistakes cost stars — they never
   block progress. Skipped clues are never lost; they are noted in your
   Case File.

## Co-op

- PeerJS (peer-to-peer, no server): create a room, share the 4-letter
  room code or `?room=XXXX&case=N` link.
- Roles (swappable between chapters): Field Agent, Analyst,
  Interrogator, Archivist. Some clues are role-exclusive and must be
  shared to the board.
- In co-op, board links need one other player to confirm; the finale is
  a party vote (everyone must agree; a tie is decided by the host).

## Honest scope — what this build is and is not

**Faithful to the design:** all 6 cases with full evidence, suspects,
timelines, twists, hints, and two-path solutions; season arc with the
Anchorman reveal in Case 6; PeerJS co-op; TTS read-aloud (device voices);
WebAudio score; stars, saves, and case file on-device.

**Web-adapted:** this is a single-page web game, not the Unity/Nakama
build. Scenes are illustrated stills with tap hotspots, not explorable
3D. "Video" cinematics are text + TTS, not rendered video — **zero
videos in this build, by design**. Suspect portraits exist only for
Case 1 (four of six suspects); Cases 2–6 use the case location art and
an emoji portrait in interrogations.

**Not in this build:** no native APK/IPA; no real voice chat (use
Discord/phone alongside the in-game text chat); no server-side
anti-cheat (nothing is enforced beyond the browser); English only;
no payments, accounts, or leaderboards; no 3D, no Vivox, no Nakama.

## Files

- `deploy/` — the shippable game: `index.html` (79 KB) + `cases/`
  (6 JSON files, 353 KB) + `images/` (15 JPGs, 3.59 MB). Total ~4.0 MB.
- `engine/index.html` — the game shell source (77 KB).
- `cases/caseN.json` + `cases/caseN-bible.md` — case data and design
  bibles (sources of truth; the deploy copies carry assembly fixes —
  see `deploy/DEPLOY-FIXES.md`).
- `images/` + `images/MANIFEST.md` — art and manifest.
- `SEASON1-BIBLE.md` — season arc + index of all six case bibles.

## Verification (2026-10-27, assembler pass)

- All 6 case JSONs independently validated: valid JSON, every
  referenced clue/suspect/option resolves, 5 chapters each, ≥20
  evidence, 3–5 red herrings, ≥1 puzzle and ≥1 interrogation per case.
  **6/6 PASS.**
- Solver test: every `minEvidenceToSolve` set exists, is key or
  supporting, resolves the solve, and is board-linkable; every bible
  documents two distinct clue paths. **6/6 PASS.**
- Headless Case 1 playthrough (jsdom, served over local HTTP):
  menu → case select (6 real titles) → real Case 1 JSON (29 evidence,
  not the fallback stub) → 5 chapters (hotspot → puzzle → interrogation
  → board link → report, all correct) → finale accusation 4/4 →
  "✅ Case Closed — The Last Ferry", 5/5 stars, 27/29 clues, zero
  uncaught JS errors. **55/55 steps PASS.**
- All 6 cases also verified loading their real JSON through the engine.

## Known gaps (see also `deploy/DEPLOY-FIXES.md`)

- `case1-suspect-5.jpg` was never generated (generation pipeline
  refused it); suspects Owen Marsh and Felix Anders have no portrait
  (the engine shows an emoji — no wrong face is substituted).
- Cases 2–6 have one location image each; every evidence/scene image
  in those cases shows that case's location art.
- Two-player live co-op sync (PeerJS join + evidence/pin sync) has not
  been tested live — solo play is fully verified; co-op code paths are
  the engine's, untested end-to-end.
- No live-browser (real Chrome) playthrough was done — verification is
  headless jsdom + live HTTP serving.
