# Leakage and Confounding Audit

## 1. Leakage Guard Checklist
* `grid_position`: Pre-race knowledge. **PASS**.
* `constructor_tier`: Pre-race knowledge. **PASS**.
* `n_stops`: **Post-race knowledge.** * **Audit Result:** PASS. This feature was strictly excluded from training for all targets. It is used only as a scenario input during What-If simulations.
* **Temporal Split:** Train (2019-21), Calib (2022), Test (2023-24). **PASS**.

## 2. Confounding Limitation
Our model assumes strategy adjustments are independent variables. However, strategy is confounded by car pace; faster cars are more likely to successfully pull off 2-stops. The model currently captures this correlation rather than pure causal effect.