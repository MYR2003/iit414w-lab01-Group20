# Baseline Comparison

This table evaluates our three models against their respective docent or naïve baselines using the 2023-2024 test set. 

| Target | Model Type | Metric | Our Model Performance | Baseline Performance | Baseline Justification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **is_top10** | Logistic Regression | Brier Score | 0.145 | 0.132 | **Docent Calibrated Model:** Provided in the capstone guidelines. Our model is slightly underperforming the heavily calibrated docent target, but easily beats the 0.208 grid-rule floor. |
| **is_top5** | Logistic Regression | Brier Score | 0.089 | 0.115 | **Top-5 Grid Rule:** A heuristic assigning an 85% probability of a top-5 finish if starting P5 or better, and 15% otherwise. Our model significantly outperforms this naive rule. |
| **finish_position** | Random Forest Regressor | MAE | 3.12 positions | 3.85 positions | **Starting Position Naïve:** A strict baseline assuming a driver finishes exactly where they started on the grid. Our model successfully reduces prediction error by nearly a full position. |

**Observation:** The regression model (`finish_position`) effectively beats the naïve baseline, proving that incorporating constructor tier alongside grid position creates a strong foundational estimate for race pace.