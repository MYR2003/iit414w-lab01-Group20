# Model Comparison Table — Lab 3

| Model | Train MAE | Test MAE | Overfit Gap | Why? (Mechanistic Reasoning) |
| :--- | :---: | :---: | :---: | :--- |
| **Baseline 1: Season Mean** | 5.86 | 5.86 | 0.00 | Predicts training-set mean for every entry. Zero sensitivity to context. The floor any useful model must clear. |
| **Baseline 2: Grid Heuristic** | 3.10 | 3.75 | 0.65 | Maps each starting position to historical average points (train set). Strong because track position is the primary constraint in F1. The true competitive baseline. |
| **Ridge Regression** | 3.07 | 3.65 | 0.58 | Linear model combining grid, team strength, driver skill. Limited by the discrete non-linear points system. Small overfit gap shows good generalisation. |
| **Gradient Boosting** | 1.90 | 3.66 | 1.77 | Sequential ensemble that directly optimises MAE step by step. Despite shallow trees (max_depth=3), the gap (1.77) exceeded RF's (1.35) — this suggests the boosting process over-adapted to training-set driver/round patterns across 100 sequential iterations. With only 320 training samples, each corrective tree had limited signal to work with, amplifying noise rather than generalising. Test MAE (3.66) is nearly identical to Ridge (3.65), suggesting the added complexity of boosting yielded no benefit over a simple linear model for this dataset size. |
| **Random Forest** ✓ | 2.24 | 3.59 | 1.35 | **Best test MAE (3.59).** Captures non-linear threshold effects invisible to Ridge — the disproportionate P1→P2 gap (25→18 pts) and the scoring/non-scoring boundary at P10→P11. Outperforms Gradient Boosting despite a smaller overfit gap, suggesting RF's parallel averaging is better suited to this dataset than GB's sequential correction. The 1.35 overfit gap remains a deployment risk if team performance rankings shift significantly mid-season. |

### Metric Summary
* **Primary Metric:** Mean Absolute Error (MAE) — interpretable in championship-point units
* **Target Variable:** `points` (0–25)
* **Validation Strategy:** Temporal Split — Train: Rounds 1–16 | Test: Rounds 17–22
* **RANDOM_SEED:** 414
