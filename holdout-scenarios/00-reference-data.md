# Reference Data (shared by holdout scenarios)

Scoring uses MVP base values: **exact = 3, correct winner = 1, correct draw = 1, miss = 0** (no bonuses/multipliers). Knockouts score on the **regulation 90-minute** result. Tiebreak: exact-score accuracy, then earliest registration.

## Dataset A — three users, three matches

**Users (registration order matters for the tiebreak):**

| User | Email | Registered |
|------|-------|------------|
| Alice | alice@example.com | T0 |
| Bob | bob@example.com | T0 + 1m |
| Carol | carol@example.com | T0 + 2m |

**Matches:**

| Match | Stage | Teams | Kickoff | Regulation result |
|-------|-------|-------|---------|-------------------|
| M1 | Group | A vs B | T0 + 10h | 2–1 (HOME_WIN) |
| M2 | Group | C vs D | T0 + 11h | 0–0 (DRAW) |
| M3 | Knockout | E vs F | T0 + 30h | 1–1, won on penalties → scored **DRAW** |

**Predictions (all placed while OPEN, before kickoff − 60m):**

| | M1 (2–1) | M2 (0–0) | M3 (1–1) |
|------|----------|----------|----------|
| Alice | 2–1 | 1–1 | 0–0 |
| Bob | 1–0 | 0–0 | 2–1 |
| Carol | 1–1 | *(none)* | 1–1 |

**Expected points (verified):**

| | M1 | M2 | M3 | Total | Exact hits |
|------|----|----|----|-------|------------|
| Alice | 3 (exact) | 1 (draw outcome) | 1 (draw outcome) | **5** | 1 |
| Bob | 1 (win outcome) | 3 (exact) | 0 (miss) | **4** | 1 |
| Carol | 0 (miss) | 0 (no pred) | 3 (exact) | **3** | 1 |

**Expected leaderboard:** 1. Alice (5), 2. Bob (4), 3. Carol (3). Sum of all points = **12** (= sum of points awarded across M1+M2+M3).

## Dataset B — tiebreak

Two users level on points; resolved by exact-score accuracy, then registration.

| User | Registered | Points | Exact hits |
|------|-----------|--------|------------|
| Dave | earlier | 4 | 1 |
| Erin | later | 4 | 0 |

**Expected:** Dave ranks above Erin (more exact hits). If exact hits were also equal, the earlier registration (Dave) would win.
