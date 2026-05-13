# Risks and Mitigations

| Risk | Observed Impact | Mitigation Strategy |
| :--- | :--- | :--- |
| **Weather Blindness** | Brier score spikes to 0.363 in high-stop races. | Integrate `is_wet` flag from FastF1 API in Hito 3. |
| **Midfield Volatility** | Tier 3 has the highest Top-10 error (0.162). | Add "Driver Form" (rolling average of last 3 finishes). |
| **DNF Bias** | Random Forest treats DNFs as P20. | Implement a two-stage hurdle model: P(Finish) then P(Position). |