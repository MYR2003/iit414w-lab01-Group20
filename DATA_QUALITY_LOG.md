# Data Quality Log — Lab 1: F1 2023 Season Analysis

## Overview
This document logs all data quality issues discovered during EDA analysis, their classification, impact assessment, and resolution decisions.

---

## Issue 1: Missing Grid Position Values

**What**: Some race entries have missing or invalid GridPosition values (e.g., pit lane starts, grid penalties).

**Classification**: MAR (Missing At Random)
- The missingness depends on race circumstances (grid penalties, pit lane starts) but not on the outcome itself.

**Impact**: 
- Affects ~3-5% of observations
- Prevents use of grid position in some predictions
- Limits training set size if row is dropped

**Decision**: Drop rows with missing GridPosition
- These represent special circumstances (DSQ, retired before race) that are hard to model
- Impact is minimal (3-5%)
- Keeping them would require special encoding (pit lane = position 99)

**Justification**:
- Grid position is the primary pre-race feature
- Special grid circumstances are difficult to predict without additional feature engineering
- For Lab 1, simpler to exclude; can revisit in Lab 2 with more sophisticated handling

---

## Issue 2: Finish Position as Numeric vs. Status Classification

**What**: FinishPosition has text values ('DNF', '+1 Lap', 'Lapped', or numeric 1-20) mixed with Status field. Some rows have null PositionText but have a Status value.

**Classification**: Type Error / Mixed Encoding
- Data encoding issue, not random missingness
- Depends on how fastf1 API returns post-race data

**Impact**:
- ~8% of rows have DNF status with null FinishPosition
- Creates ambiguity: should DNF = missing or DNF = position 99?
- Affects target variable encoding (Top10_Finish)

**Decision**: Use Status field as primary DNF indicator; convert FinishPosition to numeric, mark DNFs as NaN

**Justification**:
- Status field is more reliable than parsing PositionText
- DNF is a distinct outcome, not a finish position
- Allows for two-stage modeling later if needed (Finish → Top10)
- Consistent with domain knowledge (DNF = did not finish = no points)

---

## Issue 3: Double-Counted or Duplicate Driver Entries in Multi-Team Scenarios

**What**: One driver may appear twice in one race if they changed teams mid-season (very rare) or due to data API inconsistencies.

**Classification**: Data Entry Error / De-duplication issue
- Rarely occurs in F1 (drivers stay with one team per season)
- Could be API artifact or reserve driver fill-in

**Impact**:
- Minimal (~0.1% of rows if any)
- Affects GroupBy operations if not deduplicated
- Can skew experience counts or team statistics

**Decision**: Keep all rows; disambiguate by (Driver, RoundNumber) tuples

**Justification**:
- If legitimate (reserve drivers), removing is data loss
- If artifact, impact is negligible
- Safer to keep and document than to delete without verification

---

## Issue 4: Missing Championship Points Data for Incomplete Races

**What**: Points field is missing or zero for drivers classified outside top 10 or DNF. Cumulative points pre-race may be unavailable for early-season races.

**Classification**: MNAR (Missing Not At Random)
- Points are 0 if driver did NOT score (top 10 only get points)
- Mechanically missing based on outcome
- Cumulative points unavailable for Round 1 (no prior races)

**Impact**:
- ~55% of rows have Points = 0 (non-top-10 finishers)
- Limits point-based feature availability
- Round 1 cannot use cumulative points

**Decision**: 
- For individual race Points: treat zero as legitimate (no points scored)
- For cumulative points: backfill Round 1 with 0; use rolling cumsum from Round 2 onward

**Justification**:
- Points = 0 is correct, not missing
- Cumsum needs explicit initialization
- Allows proper temporal feature engineering

---

## Issue 5: Temporal Availability of Team/Driver History Data

**What**: Team and driver historical statistics (experience, cumulative points) are not available for early rounds or first appearances.

**Classification**: MNAR (structurally inevitable)
- Round 1 drivers have 0 prior experiences by definition
- New team debuts have 0 cumulative points by definition

**Impact**:
- Round 1 cannot use experience features
- Cannot train models that rely on prior history for first round
- Experience and cumulative stats vary widely across season

**Decision**: 
- Backfill with 0 for Round 1 and maiden appearances
- Use rolling calculations from actual race outcomes
- Mark Round 1 as potentially problematic in model evaluation

**Justification**:
- Reflects reality: first race drivers have zero experience in F1
- Backfilling with 0 is conceptually correct
- Allows full-season model inclusion; can stratify or filter if needed later

---

## Issue 6: Outliers in Driver Experience and Team Cumulative Points

**What**: Extreme values in ExperienceRaces (e.g., 50+ races for one team member) and TeamCumulativePoints (Mercedes/Red Bull far above smaller teams).

**Classification**: Outliers (Legitimate extreme values)
- Not errors; reflect real competitive disparities in F1

**Impact**:
- Affects scale/distribution of features
- May cause model training issues if not normalized
- Creates high variation in correlation estimates

**Decision**: Keep as-is; apply scaling in Lab 2 (StandardScaler/MinMaxScaler)

**Justification**:
- These are real competitive strengths, not errors
- Removal would lose information about team/driver quality
- Machine learning models handle via normalization
- For EDA, extreme values help reveal signal

---

## Summary Table

| Issue # | Column(s) | Issue Type | Classification | Decision | Rows Affected |
|---------|-----------|-----------|-----------------|----------|---------------|
| 1 | GridPosition | Missing | MAR | Drop | 3% |
| 2 | FinishPosition/Status | Encoding | Type Error | Convert + Status | 8% |
| 3 | Driver | Duplicates | Data Entry | Keep with dedup | 0.1% |
| 4 | Points | Missing/Zero | MNAR | Treat 0 as valid | 55% |
| 5 | Experience/Cumsum | Missing | MNAR (structural) | Backfill 0 | 4% (Rd 1) |
| 6 | Experience/Points | Outliers | Legitimate | Normalize in Lab 2 | 5% (extreme) |

---

## Recommendations for Lab 2

1. **Implement scaling** for GridPosition, ExperienceRaces, and cumulative points
2. **Test Round 1 impact**: build models with and without Round 1 to assess sensitivity
3. **Two-stage modeling**: consider separate (Finish → Top10) model if DNF pattern is informative
4. **Feature engineering**: construct team/driver skill proxies that handle early-season sparsity better (e.g., rolling averages, Bayesian priors)

---

**Log Updated**: March 14, 2026  
**Analyst**: Group 20  
**Data Source**: fastf1 API, 2023 F1 Season
