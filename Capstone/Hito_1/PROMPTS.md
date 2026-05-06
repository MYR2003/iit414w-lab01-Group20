# PROMPTS.md — AI Interaction Log

## IIT414W · Capstone Hito 1 · Group 20 (Martin Yunge & Benjamin Bennett)

> This document records AI-assisted interactions during the development of the F1 Race Strategy Advisor. All interactions follow the 6-field standard: **Context · Prompt · Output · Validation · Adaptations · Final Decision**.

---

## Interaction 1 — Framing the decision context and metric justification

**Context**
We needed to articulate a precise, one-sentence decision context that would satisfy the rubric's requirement of specifying who makes the decision, when in the race weekend, and what strategic question is being answered. We also needed to justify why Brier Score was the right primary metric over accuracy or F1-score.

**Prompt**

> "We are building an F1 race strategy tool for Capstone IIT414W. The tool should help a team decide whether to pursue aggressive points-seeking or conservative survival strategy. The target variable is is_top10. Help me write a one-sentence decision context and justify Brier Score as the primary metric over accuracy."

**Output**
The AI suggested the following decision context:

> *"Whether a driver will finish in the top 10, informing the Team Principal's choice between aggressive point-scoring and conservative race-survival strategy before Saturday's parc fermé."*

For metric justification, it argued that Brier Score rewards calibrated probability estimates — a model that outputs P=0.90 when the true outcome is 0 is penalised heavily, which accuracy ignores. It also noted that accuracy collapses probability into a binary and cannot distinguish a confident wrong prediction from an uncertain one.

**Validation**
We verified the Brier Score argument against the course's probability calibration notes and the scikit-learn documentation. The argument held: `sklearn.metrics.brier_score_loss` computes mean squared error between predicted probabilities and binary outcomes, making it ideal for calibrated strategy tools. We also confirmed that ROC-AUC as a secondary metric would capture rank-ordering of drivers, which the AI had not initially proposed.

**Adaptations**

- We modified the decision context to include the explicit time window ("Friday evening after Free Practice 2") and the two decision-makers ("Team Principal and Strategy Director") to satisfy the rubric's who/when requirement.
- We added ROC-AUC as a secondary metric ourselves — the AI had not suggested it.
- We removed the AI's phrasing "before Saturday's parc fermé" because parc fermé begins Saturday evening, and our decision window closes before that — we replaced with the more precise "before final car setup freeze."

**Final Decision**
We adopted the Brier Score justification verbatim with minor edits. The decision context was substantially rewritten to include all rubric-required elements. The AI's draft served as a useful scaffold but required domain correction before use.

---

## Interaction 2 — Designing the what-if comparison scenarios

**Context**
Section 4 requires at least two concrete what-if scenarios with specific feature values (driver, circuit, grid position, n_stops, compound_sequence). Our initial draft compared generic "mid-field driver" scenarios, which the partner team critique (Group 5) flagged as insufficiently specific. We used the AI to help sharpen the scenarios while keeping grid position constant.

**Prompt**

> "Our F1 strategy capstone tool predicts P(top-10) using features including grid_position, n_stops, compound_sequence, constructor_tier, and circuit_type. We want two what-if scenarios comparing 1-stop vs 2-stop strategy for the same driver, same grid position, same circuit. The driver should be a midfield constructor (tier 3 or 4). Make the scenarios as specific as possible with real F1 values."

**Output**
The AI proposed:

> *Scenario A: Esteban Ocon (Alpine, constructor_tier=4), Circuit de Barcelona-Catalunya 2023, starting P10, n_stops=1, compound_sequence=M-H → predicted P(top-10) ≈ 0.38*
> *Scenario B: Same driver, same race, same P10, n_stops=2, compound_sequence=M-H-S → predicted P(top-10) ≈ 0.29*

It also explained that Monza (high-speed, low-deg) favours 1-stop strategies while Barcelona (medium-deg) makes the 2-stop advantage less clear — and that using a real 2023 race would keep the scenario within the test set window.

**Validation**
We cross-checked the race: Ocon started P11 at the 2023 Spanish GP, not P10. The AI's specific grid position was wrong. We also noted that using a specific 2023 race is fine for illustrative purposes but we must not use the actual race outcome to validate our model in this framing document — that would be a form of test-set leakage in our reasoning.

**Adaptations — AI suggestion rejected on circuit choice**
The AI initially suggested using Monaco 2023 as the circuit because "Monaco is iconic." We rejected this: Monaco is an outlier circuit where overtaking is nearly impossible and strategy effects are dominated by qualifying position. Using Monaco would make the 1-stop vs 2-stop comparison misleading for a general strategy tool. We switched to Monza (high-speed, low-deg, genuine strategy divergence) because it provides a cleaner test of the compound_sequence effect.

We also corrected the grid position from P11 to P10 (hypothetical, kept constant across scenarios as required by Group 5's critique).

**Final Decision**
We did not use the AI's scenario verbatim. The circuit was changed from Monaco/Barcelona to Monza for domain validity. The driver (midfield constructor tier 4) was retained as the general profile but the specific scenario language was rewritten to avoid anchoring on incorrect historical data. The AI's framing of "hold grid position constant, vary strategy inputs" was structurally correct and adopted.

---

## Interaction 3 — Leakage audit cell structure for the notebook

**Context**
The rubric requires a leakage audit cell in the notebook that documents which features are pre-race, which are scenario inputs, and which are audit columns. We asked the AI to propose a structure for this cell.

**Prompt**

> "I need a Python cell for a Jupyter notebook that clearly documents feature leakage classification for an F1 race outcome prediction model. Features include: grid_position, constructor_tier, driver_experience, n_stops, compound_sequence, avg_pit_stop_duration_s, safety_car_periods, weather_outcome, qualifying_position. Classify each as pre-race, scenario input, or audit-only. Print a readable summary."

**Output**
The AI returned a clean dictionary-based classification with a pandas DataFrame display. It correctly identified `grid_position`, `constructor_tier`, and `driver_experience` as pre-race features. It classified `n_stops`, `compound_sequence`, and `avg_pit_stop_duration_s` as post-race observations / scenario inputs. It flagged `safety_car_periods` and `weather_outcome` as audit-only.

It incorrectly classified `qualifying_position` as a pre-race feature suitable for modeling.

**Validation**
The course brief (Dataset Limitations, #2) explicitly states: *"qualifying_position is a stand-in for grid_position; qualifying_time_s is empty. Treating qualifying as a real signal is a graded error."* The AI was unaware of this course-specific constraint.

**Adaptations**
We moved `qualifying_position` to the audit-only column and added a comment explaining the course brief's warning. We also added `avg_pit_stop_duration_s` explicitly to the post-race / not-for-baseline column, which the AI had placed in an ambiguous "scenario input" bucket without noting it should never appear in the baseline model.

**Final Decision**
The cell structure (dictionary → DataFrame display → printed summary) was adopted. The feature classifications were corrected per the course brief. The AI draft saved time on boilerplate but required domain-specific correction before it was rubric-safe.
