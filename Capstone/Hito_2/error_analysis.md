# Error Analysis

To understand where our models excel and fail, we sliced the test set (2023-2024) across three critical F1 contexts.

## 1. Strategy Type (Number of Pit Stops)
| Stops | MAE (Position) | Brier (is_top10) | Brier (is_top5) |
| :--- | :--- | :--- | :--- |
| 1-Stop | 3.064 | 0.151 | 0.103 |
| 2-Stop | 2.714 | 0.124 | 0.091 |
| 6-Stop | 5.543 | 0.363 | 0.172 |

**Insight:** Performance is highest for 2-stop strategies (modern F1 standard). Error spikes in 6-stop races, which represent chaotic weather events that pre-race pace cannot predict.

## 2. Circuit Type
| Circuit Type | MAE (Position) | Brier (is_top10) | Brier (is_top5) |
| :--- | :--- | :--- | :--- |
| Permanent | 2.958 | 0.132 | 0.097 |
| Semi-Street | 3.650 | 0.187 | 0.091 |
| Street | 2.953 | 0.136 | 0.100 |

**Insight:** Semi-street circuits show significantly higher error for `is_top10`, suggesting the midfield boundary is more unpredictable on hybrid layouts compared to dedicated street tracks.

## 3. Constructor Tier
| Tier | MAE (Position) | Brier (is_top10) | Brier (is_top5) |
| :--- | :--- | :--- | :--- |
| 1 (Top) | 3.504 | 0.113 | 0.195 |
| 3 (Midfield) | 3.176 | 0.162 | 0.123 |
| 5 (Backmarker) | 2.634 | 0.127 | 0.009 |

**Insight:** The model is highly accurate for backmarkers (P(Top5) is always low). It struggles most with Tier 1 cars for `is_top5`, where individual driver mistakes or reliability issues create high volatility.