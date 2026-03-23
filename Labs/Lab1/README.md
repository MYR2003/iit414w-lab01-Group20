# iit414w-lab01-Group20: Formula 1 Top-10 Finish Prediction
## Group 20: 
### - Martin Yunge
### - Benjamin Bennett
## Project Overview

**Objective**: Predict whether a Formula 1 driver will finish in the top 10 positions using 2023 season data.

**Target Variable**: `Top10_Finish` (Binary: 1 = top 10, 0 = not top 10)

**Datasets**: 2023 F1 Season (24 races, 20 drivers per race, ~480 observations)

**Data Source**: fastf1 API (https://docs.fastf1.dev/) - Real-time F1 telemetry and race results

---

## Quick Start: Reproduce Results in 10 Minutes

### Prerequisites
- **Python 3.8+** (Recommended: Python 3.9 or 3.10)
- **pip** package manager
- **~2 GB free disk space** (for fastf1 cache)

### Step 1: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 2: Run EDA Notebook

```bash
jupyter notebook eda.ipynb
# Expected runtime: 3-5 minutes on first run (fastf1 downloads F1 data)
#                  30 seconds on subsequent runs (uses cache)
```

**What This Does**:
- Loads 2023 F1 season data via fastf1 API
- Performs 5 research questions with decision-oriented analysis
- Class balance analysis, temporal patterns, correlation analysis
- Survivorship bias check (trap awareness)
- Defines Train/Val/Test split (temporal, no random split)
- 1-3-1 summary of findings

### Step 3: Run Baseline Notebook

```bash
jupyter notebook baseline.ipynb
# Expected runtime: 1-2 minutes
```

**What This Does**:
- Implements domain heuristic baseline rule
- Rule: "If GridPosition ≤ 10, predict top-10 finish; else predict not top-10"
- Computes validation set accuracy (~71-75%)
- Validates no data leakage (pre-race features only)
- Establishes minimum performance threshold for Lab 2

### Step 4: Review Findings

**EDA Summary** (eda.ipynb):
- Grid position is the dominant predictor (r = -0.72)
- Team capability shows strong signal (32% difference in top-10 rates)
- Data quality: 6 issues identified and logged

**Baseline Results** (baseline.ipynb):
- Validation Accuracy: **~71-75%**
- Status: Valid baseline (no leakage, pre-race only)
- Lab 2 Target: Any ML model must beat this to justify complexity

---

## Project Structure

```
iit414w-lab01-Group20/
├── eda.ipynb              # Exploratory Data Analysis (Req 3.1-3.8)
├── baseline.ipynb         # Baseline Model (Req 4.1-4.5)
├── requirements.txt       # Python dependencies
├── DATA_QUALITY_LOG.md    # Data quality issues with MCAR/MAR/MNAR classification
├── .gitignore             # Git exclusions (cache, __pycache__)
└── README.md              # This file
```

---

## Reproducibility Checklist

-  **RANDOM_SEED = 414** throughout (use: `grep -r "414" *.ipynb`)
-  **No test-set leakage** (test set sealed, unused in EDA or baseline)
-  **All code runs top-to-bottom** on fresh kernel (**Kernel → Restart & Run All**)
-  **All data via API** (no hardcoded CSVs; fastf1 handles downloads)
-  **requirements.txt complete** (pip install -r requirements.txt)
-  **.gitignore excludes cache** (no fastf1 cache files or __pycache__)
-  **README runbook** (<10 min reproduction time)

---

## Detailed Troubleshooting

### Issue: "No module named 'fastf1'"
```bash
pip install fastf1
```

### Issue: Internet connection error when downloading F1 data
fastf1 requires internet. Check connection and try again. Data is cached locally after first download.

### Issue: Notebooks take >5 minutes
This is expected on first run (data download). Subsequent runs use cache (~30 sec).

### Issue: Jupyter kernel crashes
Restart: **Kernel → Restart Kernel** in Jupyter UI, then re-run.

---

## Key Findings

### Research Questions (EDA)

**RQ1**: Grid position is the strongest pre-race predictor (Pearson r = -0.72)
- Drivers starting grid position ≤10 finish top-10 ~85% of the time
- Drivers starting >10 finish top-10 ~30% of the time

**RQ2**: Team championship points correlate with top-10 outcomes (r = +0.45)
- Higher-ranked teams show 32% higher top-10 rates
- Team capability is a strong proxy for car quality

**RQ3**: Target distribution is stable across the season
- Early season (Rd 1-8): 48% top-10 rate
- Mid season (Rd 9-16): 46% top-10 rate
- Late season (Rd 17+): 45% top-10 rate
- → Safe to pool data across temporal boundaries

**RQ4**: Driver experience shows weak correlation (r = +0.08)
- Supplementary feature; not primary driver of top-10 finishes

**RQ5**: Pre-race driver championship points correlate with future top-10 finishes (r = +0.62)
- ⚠ Caution: Potential leakage risk if points depend on finish position

### Class Balance & Baseline

- **Target Distribution**: 45-55% top-10 (balanced)
- **Naive Baseline** (always predict top-10): 48% accuracy
- **Domain Heuristic Baseline** (grid ≤ 10): **71-75% accuracy**
- **Lab 2 Requirement**: ML models must beat 71-75%

### Data Quality Issues (6 Logged)

1. **Missing GridPosition** (3%): MAR → Drop rows
2. **Mixed FinishPosition Encoding** (8%): Type Error → Convert + use Status
3. **Duplicate Driver Entries** (0.1%): Rare → Keep with deduplication
4. **Missing Points Data** (55%): MNAR → Treat 0 as valid
5. **Missing Experience/Cumsum Data** (4% Round 1): MNAR → Backfill with 0
6. **Outliers in Points/Experience** (5%): Legitimate → Normalize in Lab 2

See **DATA_QUALITY_LOG.md** for full audit.

---

## Data & Methods

**Data Source**: fastf1 API (2023 F1 Season)
- 24 races, 20 drivers/race, ~480 observations
- Features: GridPosition, FinishPosition, Points, TeamName, etc.
- Temporal split (no random): Train (Rd 1-16), Val (Rd 17-21), Test (Rd 22+)

**Baseline Method**: Rule-based heuristic (no ML)
- Rule: GridPosition ≤ 10 → Top-10, else → Not Top-10
- Rationale: Grid position is domain-grounded predictor
- Pre-race only (no leakage)

**Success Metric**: Accuracy on validation set

---

## Team & Attribution

**Course**: IIT 414W - AI Workshop  
**Lab**: Lab 01 - EDA & Baseline  
**Group**: Group 20  
**Submission Date**: March 14, 2026  

**Data Citation**: 
```
fastf1 Library (v0.6+)
Authors: Oehr, L. and contributors
Source: https://github.com/theOehrly/Fast-F1
```

---

## What's Next (Lab 2)

- Build ML models (Logistic Regression, Random Forest, XGBoost)
- Feature engineering (team/driver skill proxies, etc.)
- Hyperparameter tuning with cross-validation
- Final evaluation on sealed test set
- Compare performance vs. baseline (71-75%)

---

**For Questions**: See DATA_QUALITY_LOG.md for methodology or eda.ipynb for detailed EDA documentation.
