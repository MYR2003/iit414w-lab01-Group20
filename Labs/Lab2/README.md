# Lab 2: Feature Engineering + Improved Baseline

## Formula 1 Top-10 Finish Prediction

**Team**: Martin Yunge, Benjamin Bennett  
**Course**: IIT414W — Unit II · Week 4  
**Due**: Monday, March 23, 2026 · 13:50  

---

## Quick Start: Reproduce Results in 8 Minutes

### Prerequisites
- **Python 3.8+** (recommended: 3.10)
- **pip** package manager
- **~500 MB** free disk space (fastf1 cache)
- **Git** (to clone / navigate the repo)

### Step 1: Navigate to Lab 2 Directory

```bash
cd /path/to/iit414w-lab01-Group20/Lab2
```

### Step 2: Install Dependencies

```bash
pip install --quiet -r ../Lab1/requirements.txt
```

This installs:
- pandas, numpy, scikit-learn, matplotlib, seaborn
- fastf1, requests (for API data fetching)
- jupyter (for running notebooks)

### Step 3: Run the Notebook

```bash
jupyter notebook lab02_feature_engineering.ipynb
```

**Execution Time**: ~5–8 minutes on first run (fastf1 downloads F1 race data).  
Subsequent runs: ~1–2 minutes (uses cached data).

**Expected Output**:
- Model performance on validation set
- Comparison table vs Lab 1 baselines
- Error analysis with top-3 failure modes
- Generates `comparison_table.md` automatically

### Step 4: Review Results

1. **Model Performance**: See cell "Section 5: Evaluate Model on Validation Set."
2. **Comparison Table**: See `comparison_table.md` (generated in notebook).
3. **Error Analysis**: See cell "Section 8: Error Analysis — Top-3 Failure Modes."

---

## Project Structure

```
Lab2/
├── lab02_feature_engineering.ipynb    # Main notebook (all analysis)
├── comparison_table.md                # Lab 1 vs Lab 2 results (auto-generated)
├── README.md                          # This file
└── PROMPTS.md                         # AI usage documentation

```

---

## What Was Built

### Feature Engineering

**4 new features** engineered from F1 domain knowledge, with **2+ feature types**:

1. **LAG FEATURE**: `prev_position`
   - **What**: Driver's finish position in immediately previous race
   - **Why**: Recent form predicts immediate future performance
   - **Temporal**: Uses `.shift(1)` grouped by driver — no current race data
   - **Type**: Temporal lag

2. **ROLLING AGGREGATE**: `avg_position_last3`
   - **What**: Mean finish position over last 3 races
   - **Why**: Smooths anomalies; captured sustained form
   - **Temporal**: `.rolling(3, min_periods=1).shift(1)` — only past races
   - **Type**: Rolling window aggregate

3. **INTERACTION**: `driver_circuit_avg_position`
   - **What**: Driver's historical performance at each circuit
   - **Why**: Some drivers dominate specific tracks (e.g., Monaco specialists)
   - **Temporal**: Computed from training set only; frozen for validation
   - **Type**: Driver × Circuit interaction

4. **CATEGORICAL**: `tier_top`, `tier_mid`, `tier_back` (one-hot encoding)
   - **What**: Constructor tier (top/mid/back-of-grid) based on training standings
   - **Why**: Mercedes/Ferrari/RBR have faster cars → higher Top-10 rates
   - **Temporal**: Tier definition from training set only, not updated on validation
   - **Type**: Categorical encoding

All features identified against the 10-item leakage guard checklist. No race-day data used.

### Model

**Logistic Regression** (sklearn)
- Simple, interpretable baseline (no overfitting)
- Coefficients show which features matter most
- RANDOM_SEED = 414 in all `random_state=` arguments
- Handles class imbalance via `class_weight='balanced'`

### Temporal Validation

**Season-based hold-out** (matching Lab 1):
- **Train**: 2019–2022 (4 seasons, 2000+ races)
- **Validation**: 2023 (1 season, ~440 races)  
- **Test**: 2024 (sealed — not used)

No random splits. Maintains temporal discipline throughout.

### Evaluation

Reports **5 metrics** on validation set:

| Metric | What It Measures |
|--------|------------------|
| **Accuracy** | % of all predictions correct |
| **Precision** | Of predicted Top-10, what % actually finished Top-10? |
| **Recall** | Of actual Top-10 finishers, what % did we catch? |
| **F1** | Harmonic mean of precision & recall (primary metric) |
| **ROC-AUC** | Ranking quality across all probability thresholds |

---

## Results Summary

### Comparison Table

See **`comparison_table.md`** for full results. Quick summary:

| Model / Baseline | F1 Score | Improvement |
|------------------|----------|------------|
| Majority class | — | baseline |
| Domain heuristic (grid ≤ 10) | — | baseline |
| Lab 2 (Logistic Regression) | — | **vs heuristic** |

### Interpretation

**Primary Finding**: Lab 2 model [beats / matches / underperforms] the domain heuristic.

See `comparison_table.md` for the full 3–5 sentence interpretation, gap analysis, and explanation of what this means about feature quality vs. baseline rules.

### Error Analysis

**Top-3 Failure Modes identified** in "Section 8: Error Analysis":
1. Specific race/driver pairs where model diverges most from ground truth
2. Hypothesis for why the model failed in each case
3. Concrete next-step proposal (feature engineering, data collection, model type)

---

## Leakage Guard Checklist

All features verified against the 10-item leakage checklist:

1. No feature uses race-day information (position, pit stops, lap times)
2. Train/val/test split is strictly temporal (future data not in past folds)
3. No leaking of target variable or derived encodings
4. StandardScaler fitted on training data only, applied to val/test
5. .shift(1) applied to all lag and rolling features
6. Group-based features (driver-circuit, constructor tier) computed on training set only
7. No hyperparameter tuning on test set
8. Test set (2024) sealed and untouched
9. RANDOM_SEED = 414 in all random_state arguments
10. Results reproducible: notebook runs top-to-bottom on fresh kernel

---

## Reproducibility Verification

To verify reproducibility:

```bash
# Kill all kernels (if testing)
jupyter notebook --generate-config  # (if needed)

# Re-run notebook from scratch
jupyter notebook lab02_feature_engineering.ipynb
# Kernel → Restart & Run All
```

**Expected**: Same metrics (to 4 decimal places) on every run.

---

## Troubleshooting

### Issue: "ModuleNotFoundError: No module named 'fastf1'"

**Solution**: Ensure requirements.txt was installed:
```bash
pip install --quiet -r ../Lab1/requirements.txt
```

### Issue: Notebook hangs on data loading

**Reason**: First run of fastf1 downloads full F1 race data from the API (~2–3 minutes).  
**Solution**: Wait. Subsequent runs use cache (~30 seconds).

### Issue: "API failed for season 2023 at offset..."

**Reason**: Jolpica API temporary timeout or network issue.  
**Solution**: Re-run the cell. API is rate-limited (0.5 sec delay between requests), so wait time helps.

### Issue: Different results than reported in this README

**Reason**: Different random seed, or old notebook version.  
**Check**:
1. Is RANDOM_SEED = 414 in cell 1?
2. Are you running on the same Python version (or ≥3.8)?
3. Did you Kernel → Restart & Run All (not just individual cells)?

---

## File Descriptions

| File | Purpose |
|------|---------|
| `lab02_feature_engineering.ipynb` | Main notebook: data loading, feature engineering, model training, evaluation, error analysis |
| `comparison_table.md` | Auto-generated Lab 1 vs Lab 2 results table + interpretation |
| `README.md` | This file: setup, quick start, project structure |
| `PROMPTS.md` | AI tool usage log (if applicable) |

---

## Academic Integrity Statement

This submission is the original work of both team members.

 Code adheres to:**
  - Course random seed (RANDOM_SEED = 414)
  - Temporal validation discipline (no test set leakage)
  - Reproducibility standards (runs top-to-bottom on fresh kernel)
  - Feature leakage prevention (10-item checklist applied)

 Both partners understand every line of code and can explain:
  - Why each feature was engineered (domain justification)
  - How each feature avoids leakage (temporal awareness)
  - What the model performance means (honest interpretation)
  - What we would do next if the model didn't beat baselines (next-step proposals)

---

**Last Updated**: March 23, 2026 
