# Holdout Scenarios — 04 Scoring & Settlement

Covers `specs/features/04-scoring-settlement.md`. Base scoring (exact 3 / winner 1 / draw 1 / miss 0); decimal points; regulation-90 basis; deterministic and idempotent.

## Happy path
### S1 — Tiered scoring (Dataset A)
**Setup:** Dataset A; settle M1, M2, M3.
**Action:** read each user's total.
**Expected:** Alice = **5**, Bob = **4**, Carol = **3**. Specifically: exact → 3 (not 4, not 3+1), correct outcome (win/draw) wrong score → 1, miss → 0, no prediction → 0.

### S2 — Knockout decided on penalties scores on regulation
**Setup:** M3 regulation 1–1, E win on penalties.
**Action:** settle M3.
**Expected:** Carol (1–1) → 3 (exact draw); Alice (0–0) → 1 (correct draw outcome); Bob (2–1) → 0. The shootout is irrelevant.

## Edge — the key invariants
### S3 — Idempotent settlement
**Setup:** Dataset A fully settled; record totals.
**Action:** re-deliver the M1 settlement (2–1) a second time.
**Expected:** **no** change — Alice 5, Bob 4, Carol 3. M1 is not re-scored.

### S4 — Order-independence
**Setup:** two runs of Dataset A.
- Run 1: settle M1, M2, M3.
- Run 2: settle **M3, M1, M2** (different order; predictions interleaved differently).
**Action:** compare final totals and leaderboards.
**Expected:** **identical** in both runs. Final state does not depend on event order.

### S5 — Points conservation
**Setup:** Dataset A fully settled.
**Action:** sum all user totals; compare to the sum of points the rules award for M1+M2+M3.
**Expected:** equal — **12 = 12**. No points exist that aren't traceable to a settled match + a prediction.

### S6 — Cancelled match is void
**Setup:** Dataset A but M2 is **cancelled** instead of settled.
**Action:** settle M1, M3; cancel M2.
**Expected:** M2 awards **0** to everyone (no penalty); totals become Alice 4, Bob 1, Carol 3.

### S7 — Never negative & decimal
**Action:** inspect any miss and any settled total.
**Expected:** no total is ever negative (clamped at 0); points are represented as `decimal` (no floating-point artifacts).

### S8 — Determinism
**Action:** score the same `(prediction, result, rule set)` twice.
**Expected:** identical `PointsBreakdown` both times (pure function, no I/O).

## Explicit outcome assertions (SC-01, SC-03, SC-04, SC-06)
### S9 — Exact score, group stage, ×1.0 baseline (SC-01)
**Setup:** single match, group stage (multiplier = ×1.0). Alice predicts 2–1. Result is 2–1.
**Action:** settle the match.
**Expected:** Alice receives exactly **3 points** (3 × 1.0). The ×1.0 multiplier is applied and has no effect — the base value is preserved without rounding or truncation.

### S10 — Correct winner, wrong score, semi-final multiplier (SC-03)
**Setup:** single match, semi-final (multiplier = ×2.0). Alice predicts 2–0. Result is 3–0 (home win, correct winner, wrong score).
**Action:** settle the match.
**Expected:** Alice receives exactly **2 points** (1 base × 2.0). Not 3, not 1 — the winner-only outcome at a high multiplier stage.

### S11 — Correct draw, wrong score (SC-04)
**Setup:** single group-stage match. Alice predicts 1–1. Result is 0–0 (draw, wrong score).
**Action:** settle the match.
**Expected:** Alice receives exactly **1 point** (correct draw outcome, BR-001). Not 3 (that would require exact score 0–0).

### S12 — Completely wrong prediction awards zero (SC-06)
**Setup:** single group-stage match. Alice predicts 3–0. Result is 0–2 (wrong winner, wrong score).
**Action:** settle the match.
**Expected:** Alice receives exactly **0 points**. No partial credit for any field.

## Forward-only rule application
### S13 — Rule change does not retroactively affect settled matches (SC-21)
**Setup:** M1 already settled with exact-score rule = 3 pts; Alice received 3 pts. Admin now changes exact-score points to **5**.
**Action:** read Alice's total; then settle a new match M4 where Alice predicts exactly.
**Expected:** Alice's M1 points remain **3** (the rule in effect at M1's kickoff). Alice earns **5** pts for M4. Past settlements are immutable to rule changes.
