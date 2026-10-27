# Deploy fixes — Unsolved Case (assembler pass, 2026-10-27)

Every fix below was made **only in `deploy/`** (the shippable copy).
Sources under `engine/`, `cases/`, and `images/` were not modified.
Re-running the assembler (`/tmp/uc_assemble.py` + the `correct`-index
conversion) reproduces these fixes from the untouched sources.

## 1. Fetch path (deploy/index.html)
- `fetch("../cases/case"+n+".json")` → `fetch("cases/case"+n+".json")`.
  The engine file lived one level above `cases/`; in the deploy bundle
  `index.html` sits at the root next to `cases/`. Without this, every
  case silently fell back to the embedded stub.
- Fallback toast made honest: it now says the case file "could not be
  loaded — playing the built-in fallback (Case 1 outline only)" instead
  of claiming full depth. The fallback stub itself is kept as a last
  resort only.

## 2. Case menu titles were wrong (deploy/index.html)
The engine's `CASE_META` and lobby `<select>` listed placeholder titles
that do not match the written cases. Fixed to the real titles/subtitles
(from each case JSON):
- Case 2 "The Silent Gallery" → **"The Bookseller's Last Chapter"**
- Case 3 "The Winter Orchard" → **"The Final Dress Rehearsal"**
- Case 4 "The Midnight Ledger" → **"The Silent Choir"**
- Case 6 "The Thread That Binds" → **"The Anchorman"**
- Cases 1 ("The Last Ferry") and 5 ("The Glass Tide") were already
  correct. Subtitles now come from the case JSONs.

## 3. Menu hero image (deploy/index.html)
- Wired `images/hero.jpg` into the menu screen. The engine never
  referenced it; the art existed but was unused.

## 4. `correct` was a string; the engine compares by index (deploy/cases/*.json) — CRITICAL
- The engine's `answerReport` does `j === q.correct` and `answerPuzzle`
  does `i === p.correct`, where `j`/`i` are option indexes. The engine's
  own fallback stub uses `correct: 0` (an index).
- All six case JSONs stored `correct` as the **option text string**
  (e.g. `"Rosa Venn"`). Result before this fix: **no report question
  and no puzzle in any case could ever be answered correctly** — every
  answer was marked wrong, `reportWrong` incremented, stars were
  penalised, and puzzles could only be skipped after 2 failed tries.
- Fix: converted all 58 report + 16 puzzle `correct` fields from the
  string to its index in `options`. The original string is preserved in
  a new `correctText` field. Every converted value was verified to
  round-trip (`options[correct] === correctText`).

## 5. Image remap — all 147 placeholder paths pointed at files that do not exist (deploy/cases/*.json)
No image file was renamed or invented; references were remapped to the
15 real files in `deploy/images/`. After remap: **0 dangling image
references** (re-validated).

### Case 1 (14 refs → 9 distinct real files)
- Locations/scenes: `ticket-office.jpg` → `case1-harbor.jpg`,
  `mara-apartment.jpg` → `case1-apartment.jpg`,
  `police-archive.jpg` → `case1-archive.jpg`,
  `ferry-deck-night.jpg` → `case1-ferrydeck.jpg`.
- Evidence: `pier-coat.jpg` → `case1-ferrydeck.jpg`,
  `black-envelope.jpg` → `case1-archive.jpg`,
  `harbor-night.jpg` / `detective-arrival.jpg` → `hero.jpg`
  (cold-open/closing art).
- Suspects (matched by role, not by name): Rosa Venn (clerk, the only
  woman) → `case1-suspect-3.jpg`; Captain Jonas Kett → `case1-suspect-1.jpg`
  (harbourmaster portrait); Silas Bren (deckhand) → `case1-suspect-4.jpg`
  (dockworker portrait); Hattie Loom (owner) → `case1-suspect-2.jpg`
  (suited manager portrait).
- **Owen Marsh and Felix Anders → `""` (empty).** No matching portrait
  exists and `case1-suspect-5.jpg` was never generated; the engine
  renders suspects with an emoji, so an empty `img` is honest — no
  wrong face is substituted.

### Cases 2–6 (9 + 9 + 38 + 38 + 39 refs)
- Every location/scene/evidence/cold-open/closing image → that case's
  single real location file: `case2-location.jpg`,
  `case3-location.jpg`, `case4-location.jpg`, `case5-location.jpg`,
  `case6-location.jpg`. Thematic but generic: e.g. every Case 4
  evidence zoom shows the Case 4 location art.
- Every suspect portrait → `""` (no portraits exist for Cases 2–6;
  same reasoning as above — the engine shows an emoji).

## 6. Not fixed (left as designed)
- Hotspots with `finding.clueId: null` (flavor-only hotspots, e.g. 20
  in Case 1) are intentional: the engine shows their text without
  adding evidence. Left as-is.
- Clue codes that appear in hotspots of more than one chapter
  (e.g. `DIARY-01` in Case 1 ch1+ch2) dedupe via `addClue` — intended.
- Chapters without an interrogation (Case 1 ch1, ch2, ch4; similar
  gaps in Cases 4–6) render the engine's "No interrogations this
  chapter" empty state. Left as-is.
- The fallback stub's Case 2–6 titles now come from the corrected
  `CASE_META`, but its content is still the Case 1 outline — it only
  ever runs if all six real JSONs fail to load.
