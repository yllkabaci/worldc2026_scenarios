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
