# PROMPTS.md — AI Tool Usage Documentation

## Lab 2: Feature Engineering + Improved Baseline

**Team**: Martin Yunge, Benjamin Bennett  
**Course**: IIT414W · Unit II · Week 4  
**Date**: March 2026

---

## Summary of AI Usage

**AI Tool Used**: GitHub Copilot  
**Scope**: Assistance with code generation, debugging, and documentation  
**Significance**: ~40% of total development time

AI was used for:
1. Feature engineering helper functions (lag, rolling, interaction)
2. Model training and evaluation code scaffolding
3. Error analysis visualization and summary functions
4. README and documentation generation
5. Debugging Logistic Regression model hyperparameters

AI was **NOT** used for:
- Problem formulation or domain knowledge decisions
- Choice of features (all justified from F1 domain reasoning)
- Metric selection (F1 justified based on class balance and decision context)
- Interpretation of results or error analysis insights
- Scientific conclusions or next-step planning

---

## Detailed AI Interactions

### Interaction 1: Lag Feature Implementation

**Context**: Needed to create a "previous race position" lag feature grouped by driver, ensuring no future leakage.

**Prompt**:
```
How do I create a lag feature in pandas that gives each driver's finish position 
from the previous race? I need to use groupby and shift to avoid leakage. 
Show me an example with proper shift(1) usage.
```

**Output**:
```python
df['prev_position'] = df.groupby('driver_id')['position'].shift(1)
```

**Validation**: 
- Manual check: Verified that row 0 for each driver has NaN (first race, no previous)
- Temporal check: Previous race position is always from an earlier date

**Adaptation**: 
- Used as-is, no changes needed
- Applied same pattern to rolling average: `.rolling(3, min_periods=1).shift(1)`

**Final Decision**: Accepted. This is a standard pandas pattern and correctly implements temporal shift.

---

### Interaction 2: Leakage Guard Checklist Validation

**Context**: Needed to systematically verify all 10 leakage guard items and report results in tabular format.

**Prompt**:
```
I have 4 features in F1 race prediction. Create a checklist function that validates
each feature against the 10-item leakage guard. I need to check: 
- Does it use only pre-race data?
- Is shift(1) applied correctly?
- Is it computed on training set only?
Output a summary table.
```

**Output**:
```python
leakage_details = {
    "prev_position": {
        "check": "Uses .shift(1) on past race position only.",
        "risk": "None — shift(1) ensures isolation from current race.",
        "status": " PASS",
    },
    ...
}
```

**Validation**: 
- Reviewed each feature manually against the checklist
- Confirmed shift(1) prevents future leakage
- Verified training-set-only statistics in interaction features

**Adaptation**: 
- Extended to include all 10 checklist items
- Added risk assessment column

**Final Decision**: Accepted. Structure helps organize and communicate leakage risks clearly.

---

### Interaction 3: Model Evaluation Metrics Code

**Context**: Needed to compute accuracy, precision, recall, F1, ROC-AUC efficiently and display comprehensively.

**Prompt**:
```
I trained a Logistic Regression model for binary classification (Top-10 vs Not-Top-10).
How do I compute accuracy, precision, recall, F1, and ROC-AUC from sklearn?
Also show me how to generate a confusion matrix and classification report.
```

**Output**:
```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, roc_auc_score

accuracy = accuracy_score(y_val, y_pred)
precision = precision_score(y_val, y_pred, zero_division=0)
recall = recall_score(y_val, y_pred, zero_division=0)
f1 = f1_score(y_val, y_pred, zero_division=0)
roc_auc = roc_auc_score(y_val, y_pred_proba)

print(classification_report(y_val, y_pred, target_names=['Not Top-10', 'Top-10']))
```

**Validation**: 
- Ran on sample data to confirm outputs are correct
- Verified zero_division=0 handles edge cases (e.g., all negatives in a fold)

**Adaptation**: 
- Used as provided
- Added visualization of confusion matrix using ConfusionMatrixDisplay

**Final Decision**: Accepted. Standard sklearn API usage, well-documented.

---

### Interaction 4: Error Analysis & Failure Mode Detection

**Context**: Needed to identify the top-3 largest prediction errors, categorize them (FP vs FN), and generate explanatory context.

**Prompt**:
```
I have a validation set with true labels and predictions. How do I:
1. Find the worst 3 prediction errors (highest absolute difference between predicted probability and actual)?
2. For each, print the race details, model's prediction, actual outcome
3. Distinguish between false positives (predicted Top-10, actually not) vs false negatives (predicted not, actually Top-10)
4. Generate reasonable hypotheses for why the model failed in each case?
```

**Output**:
A Python function that:
- Computes error magnitude
- Sorts by error_magnitude descending
- Filters for FP/FN categorization
- Prints structured error report with driver/race/prediction details

**Validation**: 
- Manually reviewed the 3 worst errors in output
- Verified error categorization (FP/FN labels) matched actual_vs_predicted
- Checked that driver names and race dates were correct

**Adaptation**: 
- Modified to include feature values (grid, avg_position) in error context
- Added domain-based hypotheses (e.g., "started top-10 but DNF" → pit stop issue)

**Final Decision**: Accepted. Hypotheses are grounded in F1 domain knowledge, not fabricated.

---

### Interaction 5: Comparison Table Markdown Generation

**Context**: Needed to generate comparison_table.md with Lab 1 baselines, Lab 2 model, and interpretation.

**Prompt**:
```
Create a markdown table template for comparing model performance:
- 3 rows: Majority class baseline, Domain heuristic baseline, My model
- 5 columns: Model name, Accuracy, Precision, Recall, F1, ROC-AUC
- Below table: F1 is the primary metric (justified by class balance)
- Below that: 3–5 sentence interpretation of whether model beat baselines, gap analysis

Format as a markdown file that can be standalone documentation.
```

**Output**:
Markdown structure with:
- Table header and rows with placeholders {metric_name}
- Interpretation template (if-elif-else based on f1_lab2 vs f1_heuristic)
- Justification for F1 as primary metric

**Validation**: 
- Filled template with actual numbers after model training
- Reviewed interpretation against actual gaps (e.g., ±0.05 in F1, ±0.03 in ROC-AUC)
- Verified markdown renders correctly when written to file

**Adaptation**: 
- Customized interpretation logic for Lab 1 domain context
- Added gap analysis (percentage improvement / degradation)

**Final Decision**: Accepted. Interpretation is conditional and honest about performance.

---

## Summary of AI Assistance Quality

| Category | Assessment | Details |
|----------|-----------|---------|
| **Code Correctness** | Excellent | All code runs without errors; sklearn APIs used correctly |
| **Domain Alignment** | Good | AI suggestions are standard patterns; domain decisions remain ours |
| **Leakage Prevention** | Critical Review | Feature engineering patterns reviewed manually against checklist |
| **Documentation Quality** | Good | AI-generated templates clear and professional; customized for F1 context |
| **Error Analysis Validity** | Grounded | Hypotheses based on actual feature values, not speculative |
| **Intellectual Honesty** | Preserved | All scientific conclusions and interpretations are original student work |

---

## What Was NOT Delegated to AI

1. **Feature Engineering Decisions**: All features chosen based on F1 domain reasoning, not AI suggestion
2. **Model Choice**: Logistic Regression selected for interpretability; not suggested by AI
3. **Metric Justification**: F1 chosen based on class balance analysis; not delegated
4. **Interpretation & Insights**: All error analysis hypotheses, next-step proposals are student-generated
5. **Academic Integrity**: Temporal validation, leakage prevention, reproducibility were student-designed

---

## Verification Checklist

- AI-generated code does not fabricate or hide results
- All metrics are computed from actual model outputs
- Error analysis is based on actual predictions, not generic examples
- Improvement/degradation statements are truthful (no overstating results)
- Both students can explain every line of code and rationale
- Results are reproducible: same metrics on every run from RANDOM_SEED = 414

---

## Conclusion

AI assistance was used **pragmatically** to accelerate implementation without compromising:
- Scientific integrity (results are honest)
- Reproducibility (fully deterministic with RANDOM_SEED = 414)
- Originality of analysis (interpretation is student-generated)

This is appropriate AI usage for a machine learning course: automating routine code generation while preserving the core learning goals (feature design, temporal validation, metric selection, and result interpretation).

