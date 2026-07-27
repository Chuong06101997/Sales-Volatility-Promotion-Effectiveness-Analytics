# Why Are Two Stores Selling So Differently — And Should Promotions Change Because of It?

**152,460 daily transactions. 2 stores. 91 SKUs. 3 years. One question from Supply Chain: is the volatility we're seeing actually a pattern, or are we reacting to noise?**

This repository is the analysis that answered it — and the recommendations that came out the other side.

---

## Executive Summary

- **Problem:** Recurring stock-outs during peak demand, excess inventory during slow periods, planning done mostly on judgement.
- **Approach:** Audit the data, test the patterns everyone assumed were true, build a simple forecasting benchmark, recommend only what the evidence supports.
- **Headline result:** Promotions lift sales by **47% on average** versus a seasonality-adjusted baseline — with no sales dip in the 3 days after a promotion ends.
- **Forecasting benchmark:** A transparent, non-ML baseline reproduces actual sales within **28.34% WAPE** — before any advanced modeling is considered.
- **What changed as a result:** 3 targeted pilots — not a platform purchase, not a headcount request, not a 6-month roadmap.
- **What this analysis refused to claim:** ROI, causal promotion effects, and any conclusion beyond these two specific stores. Stated up front, not buried in a limitations section.

**Read next:** [Executive Summary](report/executive_summary.md) - [Full Report](report/full_report_v1.0.docx) - [Notebook 01 - Data Audit](notebooks/01_data_audit.ipynb)

---

## Key Results

| Question | Result | Evidence |
|---|---|---|
| Do promotions actually lift sales? | **+47.43%** average lift vs. seasonality-adjusted baseline (n = 12,702 promo-days) | Wilcoxon signed-rank, p < 0.001 |
| Do promotions just borrow from future sales? | No detectable dip in the 3 days after a promotion ends | Mean post-promo difference: +0.25%, not significant |
| Does store location explain the volatility? | Barely. Variance nearly identical between stores (0.989 vs. 0.982) | Brown-Forsythe test |
| Is a simple forecast good enough to start with? | 28.34% WAPE, no machine learning used | Seasonal Historical Average benchmark |
| Are weekends/seasons real sales drivers? | Yes -- weekends add **+$79.60**/day, summer/holiday months add **+$29-38**/day | OLS regression, controlling for promotions |
| Does weather matter? | No measurable effect, tested three separate ways | Confirmed null result |

## Business Recommendations

| Action | Confidence | What Happens Next |
|---|---|---|
| Focus S&OP attention on 3 specific SKU-store combinations | Moderate | Pilot underway |
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

Due to GitHub's file size limit, the full dataset is hosted on Google Drive.

📁 Full Dataset:
https://drive.google.com/file/d/1R7R_FUS5ylvMjr03TPsLGDdvBhM9-0-i/view?usp=sharing

Each notebook runs end to end against the underlying dataset -- nothing in this repository is a static write-up of numbers that can't be reproduced.

## How the Analysis Was Built

```
Business Problem -> Data Audit -> Exploration -> Hypothesis Testing -> Forecast Benchmark -> Recommendations
```

Five stages, five notebooks, in that order. Nothing was recommended before it was tested; nothing was tested before the data was audited.

---

## Why These Numbers Can Be Trusted

Three things sit behind every claim above, kept in the `report/` folder:

- **Decision Log** -- every analytical choice (what got excluded, what test was used, why) recorded at the time it was made.
- **Assumption Register** -- every assumption named, along with what breaks if it's wrong.
- **Evidence Levels** -- every finding rated Strong, Moderate, Weak, Not Supported, or Insufficient Data, so nothing is oversold.

The two-store limitation is a good example of this discipline: store, country, city, and channel are structurally tied together in this dataset, so every store-level result is scoped to *these two stores* -- not generalized to "supermarkets" or "Italy" -- and the README says so instead of letting a reader assume otherwise.

## Limitations, Stated Plainly

- Two stores only -- not enough to generalize beyond them.
- No cost data -- ROI and inventory savings are explicitly marked as not estimable, not guessed at.
- Promotion lift is associative, not causal -- no controlled experiment was possible with this data.
- The forecast benchmark hasn't been tested against actual planner accuracy yet -- that comparison data doesn't exist yet.

## Tools

Python (pandas, NumPy, SciPy, Matplotlib) inside Jupyter Notebooks. Standard statistical testing -- no framework did the thinking.

## Skills This Project Actually Required

- Catching that two "independent" stores were actually the same confounded variable, before it corrupted every downstream comparison.
- Choosing a non-parametric test because the sales data refused to be normal, and saying so.
- Turning "promotions work" into a testable hypothesis with a pre-registered baseline, instead of eyeballing a chart.
- Walking into a forecasting conversation and building the boring baseline first, on purpose.
- Writing a recommendation that a promotions team could act on without overselling what a p-value can prove.

---

## License

This project is shared for portfolio purposes. Data is synthetic/anonymized; methodology and code are original work.
