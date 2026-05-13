# Baseline Comparison

This table evaluates our three models against their respective docent or naïve baselines using the 2023-2024 test set. 

| Target | Model Type | Metric | Our Model Performance | Baseline Performance | Baseline Justification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **is_top10** | Logistic Regression | Brier Score | 0.140 | 0.132 | **Docent Calibrated Model:** Slightly underperforming the docent target but beats the 0.208 grid-rule floor. |
| **is_top5** | Logistic Regression | Brier Score | 0.097 | 0.115 | **Top-5 Grid Rule:** Heuristic assigning 85% prob to Top 5 if starting P5+. Model significantly outperforms this. |
| **finish_position** | Random Forest Regressor | MAE | 3.05 positions | 3.85 positions | **Starting Position Naïve:** Strict assumption that start = finish. Model reduces error by nearly a full position. |

**Observation:** The regression model successfully beats the naïve baseline, proving that incorporating constructor tier creates a much stronger foundational estimate than grid position alone.