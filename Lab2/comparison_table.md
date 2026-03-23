# Lab 1 vs Lab 2

## Team: Martin Yunge, Benjamin Bennett

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|-------|----------|-----------|--------|----|---------|
| Majority class | 0.5603 | 0.5603 | 1.0000 | 0.7182 | 0.5000 |
| Domain heuristic: grid <= 10 | 0.7385 | 0.7737 | 0.7538 | 0.7636 | 0.7364 |
| Lab 2 Model: Logistic Regression | 0.7069 | 0.7313 | 0.7538 | 0.7424 | 0.7793 |

## Primary Metric: F1 Score

F1 is the primary metric because both false positives and false negatives are costly in F1 team decision-making. F1 balances precision and recall equally and penalizes models that predict only one class.

## Interpretation
The Lab 2 model underperforms the domain heuristic baseline by 0.0212 F1 points. The domain heuristic remains the strongest baseline. Engineered features may be conflicting or overfitting to training patterns that do not generalize.
