# U.S. Influenza Staffing Analysis (2009–2017)

An evidence-based analysis to support medical staffing deployment across U.S. states during influenza season. The project integrates CDC mortality records with U.S. Census population estimates to compute per-capita risk by state-year, then presents the results in a three-layer Tableau dashboard designed for stakeholder decision-making.

The core methodological point: **raw death counts and mortality rate per 100,000 produce fundamentally different state-level priority rankings**, and only the rate-based ranking supports staffing decisions.

📖 **Case study writeup:** [ageelalramadhan.github.io/influenza-case-study.html](https://ageelalramadhan.github.io/influenza-case-study.html)

---

## Business Question

*Prepare for influenza season — right staff, right place, right time.* Using past trends by state and demographics, plan when and how many medical staff to deploy across U.S. states during flu season.

The project addresses three operational sub-questions:

1. Which states have the highest influenza burden after adjusting for population?
2. Has national mortality changed significantly across 2009–2017?
3. Which age groups should vaccination and outreach programs prioritize?

---

## Key Findings

- **National mortality rate has been essentially flat** at 13.8–15.8 per 100,000 across 2009–2017 (avg 14.8 per 100k ≈ 0.015%). Apparent year-over-year volume swings are largely demographic, not epidemiological.
- **Population correlates near-perfectly with raw deaths** (Pearson r = 0.956 across 458 state-year observations). This makes raw counts a near-useless cross-state comparison metric.
- **The metric choice reorders the priority map.** Five of the top-10 states by raw count drop out of the top-10 by rate. Only New York, Tennessee, Pennsylvania, Ohio, and North Carolina appear on both lists.
- **Influenza mortality is overwhelmingly age-driven.** The 85+ age group alone accounts for 51% of all deaths; the 75+ group accounts for 78%; the 65+ group accounts for 91%.

---

## Data Sources

| Dataset | Source | Grain | Rows |
|---|---|---|---|
| `CDC_Influenza_Deaths_edited.xlsx` | CDC WONDER | State × Year × Month × Age Group | 66,097 |
| `CDC_Influenza_Visits.xlsx` | CDC FluView (ILINet sentinel providers) | State × Year × Week | 24,952 |
| `CDC_Lab_Tests.xlsx` | CDC FluView (lab-confirmed strains: H1N1, H3, B) | State × Year × Week | 14,096 |
| `NIS_Flu_Shot_Survey_reduced.xlsx` | CDC National Immunization Survey | Child × demographics × vaccination | 28,466 |
| `Census_Pop_Clean` (in integrated workbook) | U.S. Census Bureau | County × Year | 28,986 |

The deaths and population datasets were merged into a single state-year master via INDEX/MATCH on a `State|Year` composite key, producing the final `Integrated` sheet (458 rows × 6 columns) used for all downstream analysis.

---

## Repository Structure

```
us-influenza-staffing-analysis/
├── Data/
│   ├── CDC_Influenza_Deaths_edited.xlsx
│   ├── CDC_Influenza_Visits.xlsx
│   ├── CDC_Lab_Tests.xlsx
│   ├── NIS_Flu_Shot_Survey_reduced.xlsx
│   └── 1_7_Data_Integration_Akeel_Alramadhan.xlsx   ← cleaned + integrated master
├── Analysis/
│   └── influenza_aggregates.py                       ← Python aggregation + correlation script
├── Docs/
│   ├── Exercise_1_2_Requirements.pdf                 ← project brief
│   └── Exercise_2_7_Spatial_Analysis_writeup.pdf     ← final analytical writeup
├── Tableau/
│   └── tableau_link.txt
└── README.md
```

---

## Data Pipeline

| Step | Tool | Action |
|---|---|---|
| 01 | Excel | Loaded CDC influenza mortality records (66,097 rows). Flagged "Suppressed" cells per CDC small-cell policy. |
| 02 | Excel · Pivot | Standardized labels, verified uniqueness on (State Code, Year, Month, Age Group), preserved suppressed-cell flags rather than imputing. |
| 03 | Excel | Cleaned U.S. Census state-population estimates (28,986 county-year rows) and rolled up to State × Year. |
| 04 | Excel · INDEX/MATCH | Built `State|Year` composite key; merged the cleaned tables into a single state-year master with population, deaths, and mortality rate. |
| 05 | Python · NumPy | Statistical validation: Pearson correlation of population vs. deaths; year-over-year rate comparison. |
| 06 | Tableau | Built three-layer dashboard: state choropleth (raw deaths) + graduated symbols (population) + symbol-color (rate per 100k). |
| 07 | Tableau Story | Story-driven dashboard tying raw-counts and rate views so staffing decisions visibly depend on metric choice. |

---

## Tools & Libraries

Excel (Pivot Tables, INDEX/MATCH) · Tableau Public · Python · Pandas · NumPy · SciPy · openpyxl

---

## Tableau Visualization

The three-layer Tableau map renders both metrics simultaneously:

- **State fill** (blue scale): total influenza-related deaths
- **Circle size**: state population
- **Circle color** (white → red): mortality rate per 100,000
- **Year filter**: 2009–2017

States that are dark *and* have red circles are highest priority — high volume *and* high per-capita risk.

**Tableau Public link:**
[Exercise 2.7 — Influenza Spatial Analysis (2009–2017)](https://public.tableau.com/views/Exercise2_7InfluenzaSpatialAnalysis20092017_AA/InfluenzaDeathsPopulation)

---

## Reproducing the Analysis

```bash
# Aggregate the integrated workbook and compute the headline statistics
python3 Analysis/influenza_aggregates.py
```

Expected output: total deaths by year, deaths by age group, state-year-level Pearson r between population and deaths, and top-10 state rankings by both metrics.

---

## Limitations

- CDC small-cell suppression policy redacts low-frequency state-month-age combinations. Suppressed cells are preserved as flags, not imputed — so per-age-group totals are underestimates of true mortality.
- Population estimates are inter-censal; not all state-year combinations are equally precise.
- Cross-state comparison is by population-adjusted rate, but not age-adjusted; some state-level variation in rate reflects age-structure differences, not behaviour or healthcare quality.
- The ILI sentinel-visit, lab-strain, and NIS vaccination datasets are included as supplementary context but are not joined into the main mortality analysis.
- 2009 influenza data overlaps the H1N1 ("swine flu") pandemic season; that year's pattern is partly an outlier.

---

## Next Steps

- Age-adjust state-level mortality rates to separate demographic structure from behavioural / healthcare drivers
- Join ILI sentinel-visit timing data to identify *when* each state's flu season typically starts, peaks, and ends — directly supporting the staffing-timing question from the project brief
- Join lab-strain data to flag years dominated by H3N2 (typically higher mortality) vs. H1N1 / B
- Use the NIS vaccination data to model whether higher childhood vaccination uptake correlates with lower elderly mortality in the same state-year (herd-effect hypothesis)

---

## Author

**Ageel Alramadhan** — Data Analyst, Hamburg
[Portfolio](https://ageelalramadhan.github.io) · [LinkedIn](https://www.linkedin.com/in/ageel-alramadhan/) · [GitHub](https://github.com/ageelalramadhan)
