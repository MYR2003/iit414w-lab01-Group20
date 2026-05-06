# F1 Race Strategy Advisor — Hito 1
### IIT414W · Group 20 · Martin Yunge & Benjamin Bennett

> **Decision tool**: Predicts P(is_top10) for an F1 driver in an upcoming race, supporting Friday-evening strategy decisions (aggressive points-seeking vs conservative race-survival).

---

## Quick start

```bash
# 1. Clone the repo
git clone https://github.com/MYR2003/iit414w-lab01-Group20.git
cd iit414w-lab01-Group20

# 2. Install dependencies
pip install -r requirements.txt

# 3. Launch the notebook
jupyter notebook Capstone/Hito_1/hito1_baseline.ipynb
```

Then select **Run All** (`Kernel → Restart & Run All`) from a clean state. The notebook runs end-to-end without manual intervention.

---

## Requirements

Python 3.9+ is required. Install all dependencies with:

```
pip install -r requirements.txt
```

Key packages: `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`, `jupyter`

---
---

## Temporal split (locked)

| Block | Seasons | Purpose |
|---|---|---|
| Train | 2019, 2020, 2021 | Fit the model |
| Calibration | 2022 | Fit isotonic calibration mapping only |
| Test | 2023, 2024 | Final evaluation — looked at once |

---

## What the notebook contains

1. **Data loading & EDA** — Load CSV, inspect target distribution, check class balance for `is_top10`
2. **Temporal split** — Partition by season into train / calibration / test blocks
3. **Leakage audit** — Classify all features as pre-race, scenario input, or audit-only
4. **Baseline model** — Logistic regression on `grid_position` + `constructor_tier`, calibrated with isotonic regression on the 2022 calibration set
5. **Evaluation** — Brier Score, log loss, ROC-AUC, and calibration curve on 2023–2024 test set
6. **Docent comparison** — Results compared against the published docent baselines (grid-rule Brier=0.208, calibrated Brier=0.132)
7. **What-if scenario** — ALO profile (constructor_tier=4), Monza, P10: 1-stop M-H vs 2-stop M-H-S

---

## Results (expected range)

| Metric | Grid-rule floor | Calibrated docent | Our target |
|---|---|---|---|
| Brier Score | 0.208 | 0.132 | 0.140–0.155 |
| ROC-AUC | — | 0.892 | 0.85+ |

---

## Who did what

| Member | Contribution |
|---|---|
| Martin Yungue | Logistic regression baseline, calibration curve, leakage audit |
| Benjamin Bennett | Random Forest ensemble, ROC-AUC comparison, cross-temporal validation |
| Group 20 | Integration, framing.md, PROMPTS.md |