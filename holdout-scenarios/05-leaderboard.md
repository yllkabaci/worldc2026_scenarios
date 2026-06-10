# Holdout Scenarios — 05 Leaderboard

Covers `specs/features/05-leaderboard.md`. Ranked by total points desc; tiebreak = exact-score accuracy, then earliest registration; filters in the MVP.

## Happy path
### L1 — Global ranking (Dataset A)
**Setup:** Dataset A fully settled.
**Action:** `GET` the global leaderboard.
**Expected:** order is **Alice (5), Bob (4), Carol (3)**, each user appearing exactly once, with rank, display name, and points.

### L2 — My ranking
**Action:** Bob requests his ranking.
**Expected:** position **2**, points **4** — consistent with the global board.

## Edge
### L3 — Tiebreak ordering (Dataset B)
**Setup:** Dave and Erin both on 4 points; Dave has 1 exact hit, Erin 0; Dave registered earlier.
**Action:** `GET` the leaderboard.
**Expected:** **Dave above Erin** (more exact-score hits). If exact hits were also equal, the earlier registration (Dave) wins. Ordering is total — no shared top rank.

### L4 — Deterministic & stable
**Action:** read the leaderboard several times without changing data.
**Expected:** byte-identical ordering each time; no duplicate user rows.

### L5 — Filters return a subset ranking
**Setup:** Dataset A; M1 & M2 are Group stage, M3 is Knockout.
**Action:** `GET` the leaderboard filtered to **stage = Group** (M1+M2 only).
**Expected:** points counted only from M1 and M2 → Alice 3+1 = **4**, Bob 1+3 = **4**, Carol 0+0 = **0**. Alice and Bob are level on 4; the tiebreak (exact hits: Alice 1 from M1, Bob 1 from M2 → equal; then earliest registration → Alice) orders **Alice, Bob, Carol**. Unfiltered (all stages) returns the L1 ordering.
