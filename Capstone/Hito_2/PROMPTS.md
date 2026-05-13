# PROMPTS.md - AI Assistance Documentation

**Context:** Implementing the expansion targets (`is_top5` and `finish_position`) and generating error analysis logic.

**Prompts:** 1. "Generate a slicing logic for error analysis using groupby for n_stops and circuit_type."
2. "How can I visualize regression quality for finish_position beyond just MAE?"

**Adaptations:** The AI suggested using `n_stops` as a training feature. I **rejected** this to prevent data leakage, moving it instead to a post-modeling "What-If" simulation to ensure the model remains a pre-race tool.

**Final Decision:** Use Logistic Regression for probabilities to maintain calibration and Random Forest for position to capture non-linear pace gaps between tiers.