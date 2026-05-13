# Leakage and Confounding Audit

## 1. Leakage Guard Checklist
* `grid_position`: Pre-race knowledge. Valid for baseline training.
* `constructor_tier`: Pre-race knowledge. Valid for baseline training.
* `n_stops` & `compound_sequence`: **Post-race knowledge.** * **Audit Result:** PASS. These features were strictly excluded from the baseline training sets for all three targets (`is_top10`, `is_top5`, `finish_position`). They are utilized *only* as user-defined scenario inputs during the What-If simulation.

## 2. The Strategy Confounding Limitation
Our current setup treats strategy adjustments (like adding a pit stop) as independent variables during simulation. This introduces a major confounding limitation: **Strategy choice is not random.** Top-tier teams with massive pace advantages can easily execute a 2-stop strategy, overtaking midfield cars in the final stint. Midfield teams attempting the exact same 2-stop strategy will get stuck in a DRS train and ruin their race. Therefore, historical data showing that "2-stops lead to higher finishes" is confounded by the fact that *faster cars are more likely to successfully pull off 2-stops*. Our models currently capture this correlation without isolating the causal effect of the strategy itself.