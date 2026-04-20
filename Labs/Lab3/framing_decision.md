# Framing Decision — F1 Points Prediction

## Business Question
A Team Principal needs to know: "How many points is this driver expected to contribute to our Constructors' Championship total this weekend?" This is critical for assessing the financial and strategic value of a driver compared to their seat cost and development budget.

## Target Variable
The target variable is `points` (numeric, 0–25+), representing the total points awarded to a driver at the end of a Grand Prix (including sprint points and fastest lap if applicable).

## Metric: Mean Absolute Error (MAE)
I chose **MAE** because it provides an interpretable "error margin" in the same units as the target. Telling a Team Principal that the model is "off by 3.2 points on average" is actionable. Unlike MSE, MAE doesn't disproportionately penalize the occasional "win" (25 pts) which is a legitimate signal in F1, making it a more honest reflection of average performance.

## Rejected Alternative
I rejected **Binary Classification (Top-10 Finish)**. While simpler, it treats a 1st-place finish (25 pts) and a 10th-place finish (1 pt) as identical successes. For a mid-field team like Alpine or Aston Martin, the difference between a podium and a single point is the difference between millions of dollars in prize money. A binary model is too "blind" to the magnitude of the outcome to support high-stakes financial decisions.