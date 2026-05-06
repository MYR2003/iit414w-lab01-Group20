# am Decision Sheet — Capstone Hito 1

### IIT414W · F1 Race Strategy Advisor · Mon May 4, 2026

> **Instructions.** Complete this sheet in your team repo as `framing.md`. Every team has 60 minutes during the studio block (14:45–15:45). Required commits: by 15:00 (sections 1–4 populated) and by 15:40 (full sheet + dataset-load notebook). No section can be left blank — write "TBD with rationale" if you are uncertain, but blank entries fail the framing rubric.

**Team name:** Group 20
**Team members:** Martin Yungue & Benjamin Bennett
**GitHub repo URL:** https://github.com/MYR2003/iit414w-lab01-Group20/

---

## 1. Decision Context

**What strategy decision is this tool supporting?**

> Whether a driver will realistically score championship points (finish top-10) in the upcoming race, determining whether the team should pursue an aggressive points-seeking strategy or a conservative race-survival/setup-testing strategy.

**Who makes this decision?**

> The Team Principal and Strategy Director on Friday evening, in conjunction with the driver to finalize pit-stop tactics and tire compound allocation before Friday night strategy meetings.

**When in the race weekend is the decision made?**

> Friday evening after Free Practice 2, once grid-position rumors are known and team performance baseline is established, but before final car setup freeze and parc fermé conditions (Saturday evening).

---

## 2. Target & Metric

**Target (LOCKED for Hito 1):** `is_top10` (binary: 1 if driver finishes in positions 1–10 earning points, 0 if positions 11+ or DNF)

**Primary metric:** Brier Score (mean squared error between predicted probability and actual outcome)

**Why this metric for this decision?**

> Brier Score measures calibration—whether a model's predicted P(top-10) actually matches real-world frequencies—critical for a strategist to trust the model's confidence. Unlike accuracy, Brier Score penalizes incorrect confidence (e.g., predicting 90% when outcome is 0%), making it honest about when the model is uncertain and when it is reliable.

**Secondary metric:** ROC-AUC to measure rank-ordering of drivers by true top-10 likelihood.

**Temporal split (LOCKED for Hito 1):**

- Train: seasons 2019, 2020, 2021
- Calibration: season 2022 (used to fit calibration mapping; never for model selection)
- Test: seasons 2023, 2024 (untouched until final evaluation)

---

## 3. Baseline Plan

**Baseline approach (one sentence):**

> Logistic regression using grid position and constructor tier as features to predict P(is_top10), with calibration mapping applied post-prediction to align probabilities with observed frequencies.

**Why is this baseline F1-defendable?** (One sentence — could you justify it without ever seeing 2023–2024 data?)

> Grid position and constructor strength are the two most transparent pre-race predictors of race outcome; a logistic regression on these features reflects the sport's fundamental performance hierarchy without risking test-set contamination.

**Direction check:** higher baseline score means higher predicted P(top10). Yes / No / Explain.

> Yes. Lower grid position number (P1 < P20) and stronger constructor (Mercedes > Haas) increase predicted probability of top-10 finish; logistic regression outputs probabilities in [0, 1].

**Expected baseline performance vs docent floor:**

- Grid-rule docent baseline: Brier = 0.208 on test
- Calibrated docent model: Brier = 0.132 on test, ROC-AUC = 0.892
- Our team's best baseline expected to land near: Brier = 0.140–0.155 (modest improvement over grid-rule, calibration-aware)

---

## 4. What-If Comparison Plan

**Strategy variables we will vary:**

- [ ] `grid_position` (pre-race, not a race-day decision)
- [X] `n_stops` (pit strategy: 1-stop vs 2-stop)
- [X] `compound_sequence` (tire strategy: M-H vs M-H-S)
- [X] `constructor_tier` (for segmentation)
- [X] `circuit_type` (to isolate strategy effects by circuit)

**Concrete scenarios to compare (at least two, with specific values):**

> Scenario A: Mid-field driver (constructor_tier=4) at a high-speed circuit (Monza, circuit_type='high_speed') starting P10, using 1-stop M-H strategy — predicted P(top-10) ≈ 0.35–0.48
> Scenario B: Same driver, same circuit, same starting position (P10), using 2-stop M-H-S strategy — predicted P(top-10) ≈ 0.28–0.40 (expected lower confidence due to additional pit exposure)

**Decision metric for the comparison:**

> Difference in calibrated P(is_top10) between Scenario A (1-stop) and Scenario B (2-stop), with 90% bootstrap confidence interval. Strategy with higher point estimate and tighter confidence bound is recommended to the strategy desk.

---

## 5. Limitations Acknowledgment

**Five known dataset limitations are documented in the Capstone Brief. Which TWO most affect our team's specific approach?**

**Limitation #1 we acknowledge: Strategy features are post-race observations (Brief Limitation #4).**

> Why it matters for our approach (1 sentence): Our tool assists Friday decisions, but the dataset contains features like `n_stops`, `compound_sequence`, and `avg_pit_stop_duration_s` known only after the race; we ensure our baseline model (grid_position + constructor_tier) uses only pre-race information, while scenario "what-if" inputs remain grounded in realistic Friday projections of strategy choices.

**Limitation #2 we acknowledge: Strategy choice is confounded with car/driver/weather (Brief Limitation #5).**

> Why it matters for our approach (1 sentence): A 2-stop strategy might appear successful only because the fastest cars frequently use them, not because the strategy itself is inherently superior for a midfield team; our model cannot infer causality, only describe historical correlations, so confidence intervals will be wide when extrapolating to new driver/car combinations.

---

## 6. Experiment Plan for Hito 1

**Three experiments we will run between today and Wednesday 16:20:**

1. **Experiment 1: Baseline calibration curve** — Fit logistic regression (grid_position + constructor_tier) on train set (2019–2021), then apply isotonic calibration on calibration set (2022). Measure Brier Score on 2023–2024 test. Success = achieve Brier ≤ 0.155 (beating the grid-rule docent floor of 0.208). Fallback: If Brier > 0.208, we will analyze feature coefficients to determine if the model over-weights grid position at the expense of constructor parity.
2. **Experiment 2: Non-linear ensemble comparison** — Train Random Forest classifier on same features (grid, constructor, driver experience). Compare ROC-AUC and Brier Score vs. logistic regression on test set. Success = Random Forest captures non-linear decision boundaries (e.g., grid position thresholds like P10→P11 point boundary) and improves ROC-AUC by >0.05.
3. **Experiment 3: Cross-temporal calibration validation** — Fit calibration on season 2022, apply to early-season 2023 races (Rounds 1–3), and track whether predicted P(top-10) aligns with actual outcomes. Success = calibration curve remains stable (no >0.10 probability shift across years), confirming the mapping generalizes despite 2022→2023 rule changes.

---

## 7. Team Workflow

**Who is doing what between now and Wednesday?**

| Member           | Owns                                             | Branch / file in repo |
| ---------------- | ------------------------------------------------ | --------------------- |
| Martin Yungue    | Logistic regression baseline + calibration curve | `Capstone/Hito_1`   |
| Benjamin Bennett | Random Forest ensemble + ROC-AUC validation      | `Capstone/Hito_1`   |
| Group 20         | Integration + cross-temporal validation          | `Capstone/Hito_1`   |

**When does each member commit by?** (We need at least one commit per member per day Tue and Wed.)

- **Tuesday 16:00:** Baseline logistic regression fitted on 2019–2021, calibration on 2022 (Brier score logged)
- **Wednesday 10:00:** Random Forest trained, ROC-AUC comparison completed
- **Wednesday 16:00:** Final cross-temporal validation run on 2023–2024 test, all results merged to main

---

## 8. Critique Received in Pair Review

> *Filled during Block 5 (15:45–16:05) after the partner team reviews this sheet.*

**Reviewing team:** Group 5

**Concrete critique we received:**

Your Section 4 varies `grid_position` (P12 vs P8) for the what-if scenarios rather than actual race strategy variables like tire compounds or pit stops. The consequence is that your tool cannot provide actionable strategy recommendations during the race because grid position is not a choice. One thing to do: Update what-if scenarios to vary strategy variables (e.g., 1-stop vs 2-stop) while keeping grid position constant.

**How we will address this critique by Wednesday:**

We have updated Section 4 to hold grid position constant (P10) while varying actionable strategy inputs (`n_stops` and `compound_sequence`) and addressed the missing dataset limitations (#4 and #5 from the Capstone Brief) in Section 5.

---

## Self-Check Before Committing

Before you push this to GitHub, verify:

- [X] Decision context is one sentence, not a paragraph
- [X] Target says exactly `is_top10` (not "Top-10" or "P(top10)")
- [X] Temporal split shows three blocks: 2019–2021 / 2022 / 2023–2024
- [X] Baseline is described in code-realistic terms (we could implement it)
- [X] What-if scenarios have specific feature values, not generic words
- [X] At least 2 of the 5 limitations are acknowledged with consequence
- [ ] PROMPTS.md exists in the repo (even if empty for now — will be populated by Wednesday)
