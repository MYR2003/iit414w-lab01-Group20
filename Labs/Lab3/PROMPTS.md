# AI Documentation (PROMPTS.md)
**IIT414W Lab 3 — Traceability-scored log**

---

### Entry 1 — Framing: Metric Selection
**Prompt:** "I need to frame an F1 prediction task as regression. What target should I use and how do I justify it to a Team Principal?"
**AI Response:** Suggested predicting `Points` using MSE.
**My critique and action:** I rejected MSE and switched to MAE. MSE squares the error, which would massively over-penalise the rare P1 win (25 pts). A P1 finish is a *legitimate* outcome, not a statistical outlier — penalising it quadratically would bias the model toward predicting the middle of the distribution. MAE treats all errors equally in points-unit terms. Implemented in **Cell 1** (`mean_absolute_error` import) and documented in `framing_decision.md` under the Metric section.

---

### Entry 2 — Bug: ValueError on Ridge/Baseline 2
**Error encountered (exact):**
```
ValueError: Input contains NaN, infinity or a value too large for dtype('float64').
```
**Where:** This appeared in **Cell 5** (Baseline 2 Grid Heuristic) when `.map(grid_map)` was applied to the test set.
**Root cause I identified:** The test set (Rounds 17–22) contained a driver starting from the pit lane (grid position not in 1–20), which had no entry in `grid_map` (computed from training only). The `.map()` returned `NaN` for that row, which then propagated into `mean_absolute_error`.
**Fix:** Added `.fillna(y_train.mean())` immediately after `.map(grid_map)` in Cell 5. This is explicitly noted in the cell's markdown comment. The fix reduced the error count from 3 to 0 NaN entries.

---

### Entry 3 — Hyperparameter Tuning: Ridge alpha and RF min_samples_leaf
**Context — Ridge alpha sweep (Cell 6b):**
I asked AI: "Which Ridge alpha should I use for this dataset?"
AI suggested alpha=0.1 as "typically better for small datasets."
I ran the sweep in **Cell 6b** across alpha=[0.1, 1.0, 10.0] and got:
- alpha=0.1: Test MAE=3.621, gap=0.654
- alpha=1.0: Test MAE=3.646, gap=0.580
- alpha=10.0: Test MAE=3.953, gap=0.339

AI's suggestion (0.1) gives marginally lower test MAE but a larger overfit gap. I chose alpha=1.0 because the +0.025 MAE difference is negligible while the smaller gap (0.58 vs 0.65) indicates better generalisation. This decision is traceable to the Cell 6b output.

**Context — RF min_samples_leaf (Cell 7):**
With default `min_samples_leaf=1`, Cell 7 showed Train MAE=0.87, Test MAE=3.96 — a gap of 3.09, indicating severe overfitting.
I asked AI: "Random Forest has Train MAE=0.87 but Test MAE=3.96. How do I reduce overfitting?"
AI suggested increasing `min_samples_leaf` to 4–8. I tested leaf=4 and got Train MAE=2.24, Test MAE=3.59, gap=1.35 — a 55% reduction in the overfit gap. The improvement is visible in the Cell 7 output. AI's suggestion worked here, but I validated it by checking the actual gap reduction rather than accepting the recommendation blindly.

---

### Entry 4 — AI Limitation Specific to This Lab
**What went wrong:** I asked AI to provide the 2023 F1 Ergast API endpoint for downloading race results. AI provided `http://ergast.com/api/f1/2023/results.json` — which returns HTTP 403 (the Ergast API was deprecated in early 2024).

**What I did instead:** I built the dataset programmatically using documented 2023 season parameters (team performance tiers, driver skill ratings, race DNF rates) under `RANDOM_SEED=414`. This is implemented in **Cells 2–3** of the notebook. The synthetic dataset reproduces the qualitative structure of 2023 (Red Bull dominance, McLaren upgrade at Round 10) while being fully reproducible without external API access.

**Critical distance:** AI's training data included the Ergast API as a valid data source. It did not know the API had been decommissioned. This is an example of AI providing outdated infrastructure information with high confidence — always verify external endpoints before building a data pipeline around them.
