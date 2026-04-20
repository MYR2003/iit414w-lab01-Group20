# Technical Memo: Predicting Championship Points Contribution
**To:** Head of Strategy  
**From:** Data Science Team  
**Date:** April 20, 2026  

### Executive Summary
We have developed a predictive tool to estimate the number of championship points a driver will earn in a given race based on pre-race data (starting position, team historical performance, and recent momentum). Our most advanced model can predict a driver's final points tally with an average error of **3.15 points**.

### How the Model Works
We compared four different approaches:
1. **The "Average" Guess:** Simply guessing the average points earned by all drivers.
2. **The "Grid Rule":** Predicting points based only on where the driver starts.
3. **The Linear Trend:** A model that looks for straight-line connections between experience and points.
4. **The Modern Approach (Random Forest):** This model acts like a committee of experts looking at complex patterns—such as how a top-tier car starting in the back (due to a penalty) recovers differently than a slower car.

### Key Findings
* **The Starting Grid is King:** Knowing only the starting position allows us to be accurate within ~3.7 points. Any AI model must significantly beat this "common sense" rule to be useful.
* **Non-Linear Gains:** Our best model (Random Forest) outperformed simple rules because it understands that moving from 3rd to 1st is worth far more points (10 extra) than moving from 13th to 11th (0 extra).

### Strategic Recommendations & Risks
* **Confidence Interval:** While the average error is ~3 points, the model is less reliable during "chaotic" races (e.g., heavy rain or high DNF rates), as it relies on 2023 season patterns.
* **Overfitting Risk:** The model currently "memorizes" certain driver/car combinations. If a team brings a major upgrade that changes their baseline speed, the model will need 2-3 races to "re-learn" their new potential.