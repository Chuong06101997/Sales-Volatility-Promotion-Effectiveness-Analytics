# Why Are Two Stores Selling So Differently — And Should Promotions Change Because of It?

**152,460 daily transactions. 2 stores. 91 SKUs. 3 years. One question from Supply Chain: is the volatility we're seeing actually a pattern, or are we reacting to noise?**

This repository is the analysis that answered it — and the recommendations that came out the other side.

---

## Executive Summary

- **Problem:** Recurring stock-outs during peak demand, excess inventory during slow periods, planning done mostly on judgement.
- **Approach:** Audit the data, test the patterns everyone assumed were true, build a simple forecasting benchmark, recommend only what the evidence supports.
- **Headline result:** Promotions lift sales by **$151.53 on average** versus non-promotion days — with no sales dip in the 3 days after a promotion ends.
- **Forecasting benchmark:** A transparent, non-ML baseline reproduces actual sales within **28.63% WAPE** — before any advanced modeling is considered.
- **What changed as a result:** 3 targeted pilots — not a platform purchase, not a headcount request, not a 6-month roadmap.
- **What this analysis refused to claim:** ROI, causal promotion effects, and any conclusion beyond these two specific stores — see "Limitations" below for why.

**Read next:** [Executive Summary](report/executive_summary.md) - [Full Report](report/full_report_v1.0.docx) - [Notebook 01 - Data Audit](notebooks/01_data_audit.ipynb)

---
<img width="978" height="725" alt="image" src="https://github.com/user-attachments/assets/8154a4b5-92c6-495b-966f-d7f030dfe1f9" />

**Caption:** promotion-day sales sit visibly higher across the boxplot than non-promotion-day sales.

**Business interpretation:** promotion days show a median sales value of $369.61 versus $260.80 on non-promotion days, and a mean of $557.66 versus $406.13 (a $151.53 mean difference). The difference is not attributable to chance (Mann-Whitney p < 0.001). The rank-biserial effect size (-0.206) indicates a small-to-moderate effect. **H1 is supported**: promotions are associated with a genuine, measurable sales lift.


## Key Results

| Question | Result | Evidence |
|---|---|---|
| Do promotions actually lift sales? | **+$151.53** average lift vs. non-promotion days (n = 12,702 promo-days vs. 139,757 non-promo-days) | Mann-Whitney U, p < 0.001 |
| Do promotions just borrow from future sales? | No detectable dip in the 3 days after a promotion ends | Mean post-promo difference: $1.50, not significant (p = 0.4255) |
| Does store location explain the volatility? | Barely, in practical terms. Volatility close between stores (CoV 0.1097 vs. 0.1147) | Brown-Forsythe test |
| Is a simple forecast good enough to start with? | 28.63% WAPE, no machine learning used | Seasonal Historical Average benchmark |
| Are weekends/seasons real sales drivers? | Yes -- Weekends add +$79.09/day, and Summer shows the highest seasonal sales ($442.77 vs. Spring's $399.14). | Mann-Whitney U (weekend); Kruskal-Wallis (season) |
| Does weather matter? | No measurable weather effect across non-parametric tests. | Confirmed null result |



## Business Recommendations

| Action | Confidence | What Happens Next |
|---|---|---|
| Apply manual adjustment to promotion-day forecasts, where error is concentrated | Strong | Pilot underway |
| Test a modest increase in promotion frequency | Moderate | Controlled pilot, margin tracked |
| Run the forecast benchmark alongside current planning | Not yet provable it's better | Parallel test, not a replacement |

No recommendation here involves buying software, replacing a planner, or committing to a revenue number the data can't back up.

---

## Where the Analysis Lives

```
notebooks/01_data_audit.ipynb                -> is the data trustworthy?
notebooks/02_exploratory_data_analysis.ipynb -> what patterns are worth testing?
notebooks/03_hypothesis_testing.ipynb        -> which patterns are real?
notebooks/04_forecasting_benchmark.ipynb     -> what does a simple baseline achieve?
notebooks/05_business_recommendations.ipynb  -> what should happen next?
report/                                      -> executive summary, full report, decision log
```
## Dataset

The complete dataset (**152,460 daily transactions**) is available via Google Drive.

GitHub's file size limit prevents the full Excel file from being stored directly in this repository.

📁 Full Dataset:

https://drive.google.com/file/d/1R7R_FUS5ylvMjr03TPsLGDdvBhM9-0-i/view?usp=sharing

All analyses and reported results in this repository are reproducible using the complete dataset linked above.

## How the Analysis Was Built

```
Business Problem -> Data Audit -> Exploration -> Hypothesis Testing -> Forecast Benchmark -> Recommendations
```

Five stages, five notebooks, run in that order — each stage only uses what the previous one confirmed.

---

## Methodology & Governance

Three things sit behind every claim above, kept in the `report/` folder:

- **Decision Log** -- every analytical choice (what got excluded, what test was used, why) recorded at the time it was made.
- **Assumption Register** -- every assumption named, along with what breaks if it's wrong.
- **Evidence Levels** -- every finding rated Strong, Moderate, Weak, Not Supported, or Insufficient Data, so nothing is oversold.

The two-store limitation is a good example: store, country, city, and channel are structurally tied together in this dataset, so every store-level result is scoped to these two stores, not "supermarkets" or "Italy" in general.

## Limitations, Stated Plainly

- Two stores only -- not enough to generalize beyond them.
- No cost data -- ROI and inventory savings are explicitly marked as not estimable, not guessed at.
- Promotion lift is associative, not causal -- no controlled experiment was possible with this data.
- The forecast benchmark hasn't been tested against actual planner accuracy yet -- that comparison data doesn't exist yet.

## Tools

Python (pandas, NumPy, SciPy, Matplotlib) inside Jupyter Notebooks. Standard statistical testing -- no framework did the thinking.

## What This Project Required

- Catching that two "independent" stores were actually the same confounded variable, before it corrupted every downstream comparison.
- Choosing a non-parametric test because the sales data refused to be normal, and saying so.
- Turning "promotions work" into a testable hypothesis with a pre-registered baseline, instead of eyeballing a chart.
- Walking into a forecasting conversation and building the boring baseline first, on purpose.
- Writing a recommendation that a promotions team could act on without overselling what a p-value can prove.

---

## License

This project is shared for portfolio purposes. Data is synthetic/anonymized; methodology and code are original work.
