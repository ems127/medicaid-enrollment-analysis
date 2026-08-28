# Medicaid Enrollment & the 2023 Unwinding: A State-Level Analysis

## Overview

This project evaluates how the 2023 end of continuous Medicaid coverage ("the unwinding") affected state Medicaid enrollment, and whether the effect differed between states that expanded Medicaid under the ACA and states that did not. It combines descriptive statistics, causal-inference methods (difference-in-differences with parallel-trends validation), a sensitivity analysis, and a time-series forecast.

An interactive companion dashboard is available here: **[View on Tableau Public](https://public.tableau.com/views/MedicaidEnrollmentAnalysis_17879579316970/MedicaidExpansionandEnrollment?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)**

## Research Questions

1. Do Medicaid expansion states have a different enrollment rate (per 1,000 residents) than non-expansion states?
2. Did the gap between expansion and non-expansion states change after the 2023 unwinding began?
3. Is that change associated with the unwinding, or does it reflect a pre-existing trend?
4. Where is enrollment headed over the next 12 months for each group?

## Data Sources

- **Medicaid and CHIP Eligibility Operations and Enrollment Snapshot** — CMS, [data.medicaid.gov](https://data.medicaid.gov). Monthly, state-level, September 2013–present.
- **State population estimates** — U.S. Census Bureau, Population Estimates Program (PEP).

Data was restricted to 2017 onward (earlier years had inconsistent reporting) and to Medicaid-only enrollment (CHIP excluded).

## Methodology

**1. Data cleaning (R)** — filtered to finalized monthly reports only, removed footnote/metadata columns, converted reporting periods to true dates, joined enrollment data to Census population estimates by state and year, and computed an enrollment-per-1,000-residents rate.

**2. Descriptive comparison** — visualized annual and monthly enrollment trends by expansion status, then confirmed the observed gap with a Welch's two-sample t-test comparing mean enrollment rate between expansion and non-expansion states across the full study period.
- Non-expansion states: 158.0 per 1,000
- Expansion states: 237.5 per 1,000
- Difference: 79.5 (95% CI: 76.8–82.2), t = -57.71, p < .001

**3. Difference-in-differences (DiD)** — tests whether the expansion/non-expansion gap changed after the unwinding began (April 2023 cutoff), controlling for state population and using state-clustered standard errors to account for repeated monthly observations per state.
- Pre-unwinding gap: 83.9 per 1,000 (84.96 controlling for population)
- Post-unwinding gap: ~69.5 per 1,000 (~71.5 controlling for population)
- Estimated narrowing: −14.4 per 1,000 (−13.5 controlling for population); p = .082 and p = .094 respectively with state-clustered standard errors — a meaningful but not conventionally significant reduction in the expansion advantage.

**4. Parallel-trends validation** — tested whether expansion and non-expansion states were already on different trajectories *before* the unwinding, a key assumption underlying the DiD design.
- Pre-period interaction (expansion status × month), state-clustered standard errors: −0.04 (p = .84) — no evidence of a pre-existing differential trend, supporting the validity of the DiD comparison.

**5. Sensitivity analysis** — re-estimated the DiD model (a) excluding a buffer window (Feb–May 2023) around the cutoff, and (b) using an alternative cutoff date (July 2023), to check whether results depended on the exact date chosen.
- Buffer-excluded estimate: −13.5 (p = .103)
- Alternative cutoff estimate: −14.3 (p = .085)
- Both are similar in magnitude to the main specification, indicating the finding is not highly sensitive to the exact cutoff date, though statistical significance remains borderline across specifications.

**6. Forecast** — fit automated exponential smoothing (ETS) models separately for expansion and non-expansion states, trained on post-unwinding data only, projecting 12 months forward.
- Expansion states: ETS(M,A,N) — non-damped trend, projecting continued decline at a fairly constant rate.
- Non-expansion states: ETS(M,Ad,N) — damped trend, projecting decline that gradually levels off.
- Both project continued enrollment decline in the near term, consistent with ongoing post-unwinding redetermination activity.

## Key Finding

Medicaid expansion states maintain a significantly higher enrollment rate than non-expansion states, but that advantage narrowed by an estimated 13–14 enrollees per 1,000 residents after the 2023 unwinding began — a change not explained by a pre-existing trend (confirmed via parallel-trends testing), and one that appears to be continuing based on near-term forecasts. The result is directionally robust to alternative cutoff specifications and a population control, though its statistical significance is borderline (p ≈ .08–.10) once accounting for state-clustered standard errors.

## Limitations

- The DiD interaction is marginal, not conventionally significant at the 0.05 level — results should be described as suggestive, not conclusive.
- The forecast is trained on roughly two years of post-unwinding data, which limits confidence in extrapolating trends further out; confidence intervals widen accordingly.
- This dataset is state-aggregate; no individual-level demographic data (race, income) was available. Age-based breakdown (child vs. adult enrollment) is a planned extension.
- Difference-in-differences designs identify association with the timing of a policy change, not definitive proof of causation — other concurrent events could contribute to the observed pattern.

## Repository Structure

```
├── README.md
├── medicaid-enrollment-analysis.Rmd     (full analysis: cleaning through forecast)
├── medicaid-enrollment-analysis.html    (rendered report with plots and output)
└── data/
    └── cleaned_enrollment.xlsx
```

## Tools

R (dplyr, tidyr, stringr, lubridate, readr, writexl, readxl, ggplot2, lmtest, sandwich, forecast), Tableau Public

## Author

Evelynn Michaud-Spangler — [LinkedIn](https://www.linkedin.com/in/evelynn-michaud-spangler/)
