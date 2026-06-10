# Holdout Scenarios — 02 Matches

Covers `specs/features/02-matches.md`. Includes cancel/postpone and audited re-settlement (in the MVP).

## Happy path
### M1 — Calendar & details
**Setup:** Dataset A fixtures loaded.
**Action:** `GET` the calendar; then `GET` match M1 by id.
**Expected:** calendar lists M1–M3 with stage, teams, kickoff (UTC), and status; filtering by stage=Group returns M1, M2 only. Match detail for M1 returns its data; an unknown id returns `404`.

### M2 — Admin sets official result → triggers settlement
**Setup:** M1 is `Finished`; predictions from Dataset A exist.
**Action:** an **admin** sets M1's result to 2–1.
**Expected:** the match transitions to settled, the result is recorded, and scoring runs (see `04`): Alice +3, Bob +1, Carol +0.

## Error / authorization
### M3 — Non-admin cannot set result
**Action:** a `User` attempts to set M1's result.
**Expected:** `403`; no result recorded, no scoring.

### M4 — Unknown / not-settleable match
**Action:** admin sets a result for a non-existent match id; then for a match still `Upcoming`.
**Expected:** `404` for the unknown id; `409` (`MatchNotSettleable`) for the not-yet-eligible match.

## Edge — lifecycle
### M5 — Cancel voids predictions
**Setup:** predictions exist for M2.
**Action:** admin **cancels** M2.
**Expected:** M2 becomes `Cancelled`; on settlement its predictions are **void** — every user gets **0** for M2, no penalty (BR-008). (In Dataset A this leaves Alice 4, Bob 1, Carol 3 if M2 is cancelled instead of settled — verify the delta is exactly the M2 points removed.)

### M6 — Postpone resets the deadline
**Setup:** M1 kickoff = T0+10h, deadline = kickoff − 60m; current time is just past the original deadline.
**Action:** admin **postpones** M1 to T0+20h.
**Expected:** the prediction deadline is recomputed to the new kickoff − 60m; existing predictions are kept; a user may now create/modify a prediction again because the window is open relative to the new kickoff.

### M7 — Audited re-settlement
**Setup:** M1 already settled at 2–1 (Alice +3).
**Action:** admin re-records M1 as 1–1 **with a single confirmation**; then **with the required second confirmation**.
**Expected:** the single-confirmation attempt is **rejected** (no change). The double-confirmed change writes an **immutable audit entry** and **re-runs settlement**, correcting totals (Alice's M1 now scores as a draw-outcome = 1, not 3; her total drops by 2). The audit trail records actor, before/after, and timestamp.
