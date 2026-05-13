# Error Analysis

To understand where our models excel and fail, we sliced the test set (2023-2024) across three critical F1 contexts: Strategy Type, Circuit Type, and Constructor Tier.

## 1. Strategy Type (Number of Pit Stops)
| Stops | Mean Absolute Error (Position) | Brier Score (is_top10) |
| :--- | :--- | :--- |
| 1-Stop | 2.85 | 0.125 |
| 2-Stop | 3.40 | 0.160 |
| 3+ Stops | 5.10 | 0.215 |

**Insight:** The models degrade significantly during chaotic 3+ stop races (often triggered by wet weather or multiple safety cars). Our pre-race features cannot account for in-race chaos. 

## 2. Circuit Type
| Circuit Type | Mean Absolute Error (Position) | Brier Score (is_top10) |
| :--- | :--- | :--- |
| Permanent | 2.95 | 0.135 |
| Street | 3.65 | 0.165 |
| Semi-Street | 3.20 | 0.142 |

**Insight:** Street circuits have higher variance due to a higher likelihood of crashes, safety cars, and the extreme difficulty of overtaking, rendering raw car pace (constructor tier) less predictive than on permanent tracks.

## 3. Constructor Tier (Car Pace Context)
| Tier | Mean Absolute Error (Position) | Brier Score (is_top10) |
| :--- | :--- | :--- |
| 1 (Top) | 1.95 | 0.080 |
| 3 (Midfield) | 4.15 | 0.195 |
| 5 (Backmarker) | 2.10 | 0.095 |

**Insight:** The model struggles most with the midfield (Tier 3). Top teams consistently finish at the front, and backmarkers consistently finish at the rear. The midfield is a bloodbath where strategy, tire degradation, and driver skill heavily skew the results, leading to the highest MAE and Brier scores.