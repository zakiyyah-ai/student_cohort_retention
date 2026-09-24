# Student Cohort Retention Dashboard

An interactive Tableau dashboard that answers the question every ed-tech operator
eventually asks: **"Which cohorts of students stick around, and when do they
start dropping off?"**

**[→ View the live dashboard on Tableau Public](https://public.tableau.com/app/profile/zakiyatun.surya/viz/StudentCohortRetentionDashboard/StudentCohortRetention)**

![Tableau](https://img.shields.io/badge/Tableau-Public-E97627?logo=tableau&logoColor=white)
![Python](https://img.shields.io/badge/Python-pandas-3776AB?logo=python&logoColor=white)
![License](https://img.shields.io/badge/data%20license-CC--BY%204.0-blue)

---

## What this shows

Using weekly Virtual Learning Environment (VLE) activity as a proxy for
engagement, the dashboard tracks **22 course cohorts** (grouped by intake
period — February vs. October presentations) across their first 30 weeks:

- **Cohort retention heatmap** — rows are cohorts, columns are weeks since
  start, color is % of students still active that week. The classic
  cohort-analysis visual used across SaaS and product analytics.
- **Retention curve comparison** — one line per cohort, so intake periods can
  be compared directly (e.g. do October intakes retain better than February
  intakes?).
- **Weekly drop-off chart** — surfaces exactly which week sees the sharpest
  decline in engagement, which is usually the most actionable finding for an
  operations team (e.g. "engagement drops hardest right after the first
  graded assessment").

## Dataset

This project uses the **[Open University Learning Analytics Dataset
(OULAD)](https://analyse.kmi.open.ac.uk/open-dataset)** — a public dataset
covering 32,593 students, 22 course-presentations, and 10.6 million VLE
interaction records, released under CC-BY 4.0.

> Kuzilek J., Hlosta M., Zdrahal Z. *Open University Learning Analytics
> dataset.* Sci. Data 4:170171. doi: 10.1038/sdata.2017.171 (2017).

Raw files used: `courses.csv`, `studentInfo.csv`, `studentRegistration.csv`,
`studentVle.csv`.

## Repo structure

```
student-cohort-retention-dashboard/
├── build_cohort_retention.py   # ETL: raw OULAD CSVs -> cohort retention table
├── cohort_retention.csv        # Output: cohort x week x retention%, ready for BI tools
├── StudentCohortRetention.twbx # Tableau packaged workbook
└── README.md
```

## Methodology

1. **Cohort definition** — each `code_presentation` (e.g. `2013J`) is treated
   as an intake cohort. OULAD's own convention marks "B" presentations as
   starting in February and "J" presentations as starting in October.
2. **Weekly activity** — a student is counted as "active" in week *N* if they
   logged at least one VLE click during that 7-day window since day 0 of
   their course presentation.
3. **Cohort base** — students who unregistered *before* day 0
   (`date_unregistration < 0`) are excluded, since they never actually
   started the course.
4. **Retention %** — active students in week *N* ÷ total cohort size.

Full logic lives in [`build_cohort_retention.py`](./build_cohort_retention.py).

## Running the ETL yourself

```bash
pip install pandas

# Point --data-dir at a folder containing the 4 OULAD CSVs above
python build_cohort_retention.py --data-dir ./oulad_csv --out cohort_retention.csv
```

Output columns: `code_module`, `code_presentation`, `cohort_label`,
`week_number`, `active_students`, `cohort_size`, `retention_pct`.

## Key insight

- **Week 10 is the sharpest drop-off point** — the average cohort loses 9.3
  percentage points of engagement that week, before partially rebounding
  (+7.6pp) by week 12. This likely lines up with the first graded assessment
  across most modules.
- **Retention varies widely by cohort**: `AAA · 2013J` holds the highest
  average retention (74%), while `GGG · 2014B` sits at 46.6% — a 27-point
  gap between the best- and worst-performing cohorts.
- **October intakes (J) retain slightly better than February intakes (B)**
  on average (58.1% vs. 55.4%), consistent across most modules.

## Tech stack

- **Python (pandas)** — ETL, cohort construction, weekly activity aggregation
- **Tableau Public** — visualization & publishing

## Author

**Zakiyatun Surya** — Operations Specialist, Automation & Internal Tooling
[Portfolio](https://zakiyyah-ai.github.io) · [LinkedIn](https://www.linkedin.com/in/zakiyatun)
