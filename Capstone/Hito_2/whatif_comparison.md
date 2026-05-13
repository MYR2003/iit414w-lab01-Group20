# What-If Strategy Comparison

**Scenario:** Tier 3 Driver (Midfield) starting P8 at a Permanent Circuit.

| Strategy Choice | P(is_top10) | P(is_top5) | Predicted Position |
| :--- | :--- | :--- | :--- |
| **Conservative 1-Stop** | **0.84** | 0.12 | 8.1 |
| **Aggressive 2-Stop** | 0.78 | **0.26** | 6.8 |

**Substantive Recommendation:**
If using only `is_top10`, the recommendation is the **1-Stop** (84% vs 78%). However, the expansion target `is_top5` reveals that the **2-Stop** more than doubles the chance of a high-value result (26% vs 12%). For a midfield team looking for a "breakout" result rather than safe points, the 2-stop is the superior choice. This trade-off is invisible without multi-target modeling.