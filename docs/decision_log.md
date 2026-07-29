# Decision Log

**Project:** Sales Volatility, Promotion Effectiveness & Forecasting Benchmark Review
**Status:** Canonical governance document. Covers Notebooks 01-05.
**Purpose:** Every important analytical decision made in this project, and why it was made, so that any question of "why this method and not another" has a documented answer.

This document records decisions. It does not restate results — results live in the notebooks. Where a number is referenced below, it is cited from the notebook that produced it, not recalculated here.

---

## Decision 01

**Decision ID:** D01
**Notebook:** 01 — Data Audit
**Business Question:** Is the dataset trustworthy enough to build analysis on?
**Issue Observed:** One row (STORE0002, SKU0004, 2021-09-12) contained ten missing fields simultaneously — every commercial and supply field except the identifying columns (date, store, SKU, units_sold).
**Possible Options Considered:**
- Impute the missing fields using column means or a model-based estimate.
- Leave the row in place with missing values, letting downstream operations handle nulls implicitly.
- Remove the single row from analysis.
**Chosen Decision:** Remove the row.
**Reason:** The pattern (ten fields missing at once, on the exact date a product's history at that store ends) is consistent with a truncated end-of-history record, not a recoverable data-entry gap. Imputing ten interdependent commercial fields (price, discount, sales, margin, stock) for one row would fabricate data that cannot be verified against anything.
**Evidence Supporting the Decision:** The row's date coincides exactly with the last day SKU0004 has any recorded activity at STORE0002 (confirmed in Notebook 01's date-integrity and product-continuity checks). At 1 row out of 152,460 (0.0007%), removal has no material effect on any downstream statistic.
**Impact on Later Analysis:** Every subsequent notebook loads the dataset with this single row excluded (`net_sales.notna()`), applied identically in Notebooks 02–05.
**Would This Decision Be Revisited?** No, unless a future data refresh explains the missing values directly (e.g., a corrected source file), in which case the row could be restored rather than imputed.

---

## Decision 02

**Decision ID:** D02
**Notebook:** 03 — Hypothesis Testing
**Business Question:** Do promotions generate genuine sales lift?
**Issue Observed:** Net sales are strongly right-skewed (raw-scale skew ≈ 1.9–2.0), and a Shapiro-Wilk normality check rejects normality even on a reduced sample.
**Possible Options Considered:**
- Independent samples t-test (promotion vs. non-promotion days).
- Welch's t-test (relaxes the equal-variance assumption but still assumes approximate normality).
- Mann-Whitney U test (no normality assumption).
**Chosen Decision:** Mann-Whitney U test.
**Reason:** The t-test family assumes the underlying distributions are approximately normal, or that the sample size is large enough for the Central Limit Theorem to make the *mean* approximately normal — but with a skew this strong and a comparison group this large (12,702 vs. 139,757), a mean-based test risks being driven by the long right tail rather than a representative central tendency. Mann-Whitney compares distributions via ranks and carries no such assumption.
**Statistical Justification:** Confirmed non-normal both via Shapiro-Wilk (p<0.001 on a 5,000-row sample) and via skew/kurtosis diagnostics on the full column.
**Evidence Supporting the Decision:** Notebook 03, Section 3.
**Impact on Later Analysis:** Every subsequent group comparison in Notebook 03 (weekend, weather-rain) uses the same Mann-Whitney approach for consistency, rather than mixing test families across sections.
**Would This Decision Be Revisited?** Only if a future dataset version shows a materially less skewed sales distribution, which would make a t-test defensible and potentially more powerful.

---

## Decision 03

**Decision ID:** D03
**Notebook:** 03 — Hypothesis Testing
**Business Question:** Do weekend sales differ from weekday sales?
**Issue Observed:** Same underlying net_sales distribution as Decision 02 — right-skewed, non-normal.
**Possible Options Considered:**
- Independent t-test.
- Mann-Whitney U test.
**Chosen Decision:** Mann-Whitney U test.
**Reason:** For consistency with Decision 02 and because the same non-normality diagnostic applies to this comparison — there is no reason to use a different test family for a structurally identical two-group comparison on the same outcome variable.
**Evidence Supporting the Decision:** Notebook 03, Section 5.
**Impact on Later Analysis:** Establishes a general project convention: any unpaired two-group comparison on net_sales uses Mann-Whitney, not a t-test, unless a specific reason to deviate is documented.
**Would This Decision Be Revisited?** No, this follows directly from Decision 02's justification and does not need independent re-litigation.

---

## Decision 04

**Decision ID:** D04
**Notebook:** 03 — Hypothesis Testing
**Business Question:** Does season affect sales?
**Issue Observed:** Four groups to compare (Winter, Spring, Summer, Autumn), same non-normal net_sales distribution.
**Possible Options Considered:**
- One-way ANOVA.
- Welch's ANOVA (relaxes equal-variance assumption, still assumes approximate normality).
- Kruskal-Wallis test (rank-based, no normality assumption, generalizes Mann-Whitney to more than two groups).
**Chosen Decision:** Kruskal-Wallis test, with Dunn's post-hoc test (Bonferroni-corrected) for pairwise follow-up.
**Reason:** ANOVA assumes normally distributed residuals within each group; the same skew that ruled out a t-test for two groups rules out ANOVA for four. Kruskal-Wallis is the direct multi-group extension of the Mann-Whitney approach already adopted (Decision 02/03), keeping the project's test family consistent.
**Statistical Justification:** Kruskal-Wallis rejected the null of equal distributions (H=206.59, p<0.001); Dunn's test was then required specifically because Kruskal-Wallis only establishes that *some* group differs, not *which* — a business recommendation naming Summer and Spring specifically required the pairwise follow-up.
**Evidence Supporting the Decision:** Notebook 03, Section 6.
**Impact on Later Analysis:** The specific pairwise result (Autumn vs. Winter not distinguishable, all other pairs significant) directly shaped the wording of the seasonal finding in Notebook 05 ("driven by Summer and Spring specifically," not "all four seasons differ").
**Would This Decision Be Revisited?** No, unless the underlying sales distribution changes materially.

---

## Decision 05

**Decision ID:** D05
**Notebook:** 03 — Hypothesis Testing
**Business Question:** Does weather affect sales?
**Issue Observed:** Prior exploratory work (Notebook 02) suggested a visually flat relationship between sales and both temperature and rainfall — a plausible case for skipping formal testing altogether.
**Possible Options Considered:**
- Skip formal testing, relying on the exploratory scatterplots and simple correlations from Notebook 02.
- Test formally anyway, accepting that the likely result is "no effect."
**Chosen Decision:** Test formally (Kruskal-Wallis for temperature category, Mann-Whitney for rain category), despite the expected weak signal.
**Reason:** An exploratory visual impression is not evidence — the project's own governance principle (evidence before conclusion) applies equally to expected null results as to expected positive ones. A business claim of "weather doesn't matter" needs the same statistical backing as a claim that it does; asserting a null result without testing it would be exactly the kind of unverified claim this project's methodology exists to prevent.
**Evidence Supporting the Decision:** Notebook 03, Section 7 (temperature p=0.325, eta-squared ≈ 0.000003; rain p=0.665) — the formal test confirms, rather than merely assumes, the exploratory impression.
**Impact on Later Analysis:** Notebook 05 explicitly excludes weather from recommended planning inputs, citing this tested (not assumed) result.
**Would This Decision Be Revisited?** Yes, if a future dataset includes more granular or lagged weather variables not tested here — the current finding applies to same-day temperature and rainfall only.

---

## Decision 06

**Decision ID:** D06
**Notebook:** 03 — Hypothesis Testing
**Business Question:** Does sales volatility differ between the two stores?
**Issue Observed:** Volatility can be defined multiple ways — raw transaction-level variance, log-transformed variance, coefficient of variation on units sold, or coefficient of variation on aggregated daily totals — and different definitions produced materially different-looking answers during earlier project iterations (e.g., raw transaction-level net_sales variance showed a large, scale-driven gap between stores that did not survive a price/scale adjustment).
**Possible Options Considered:**
- Raw transaction-level net_sales variance.
- Log-transformed transaction-level variance (scale-adjusted).
- Coefficient of variation of transaction-level units_sold.
- Coefficient of variation of **daily aggregated total sales**, per store.
**Chosen Decision:** Coefficient of variation of daily aggregated total sales per store, tested via Brown-Forsythe.
**Reason:** A store-level volatility question is operationally a question about *day-to-day total demand at that store*, not about the dispersion of individual transaction line items — the two are related but not the same thing, and reporting multiple definitions side by side in earlier drafts risked giving three different "answers" to what should be one business question. Daily aggregation matches how a Supply Chain planner actually experiences volatility (as a daily total to plan against), and CoV expresses that volatility on a scale-independent, business-interpretable basis (a percentage of the mean).
**Why Previous Definitions Were Discarded:** Raw transaction-level variance is heavily distorted by each store's product-price mix (confirmed in an earlier iteration: STORE0002 showed a raw variance ratio of roughly 0.52 relative to STORE0001, driven mechanically by STORE0002 carrying fewer, lower-priced SKUs, not by genuinely different volatility). Reporting that number without the aggregated, scale-adjusted view would have been misleading. To avoid the project maintaining two or three competing "volatility" numbers, a single canonical definition (daily total CoV) was adopted and all others retired from the reported results.
**Evidence Supporting the Decision:** Notebook 03, Section 8 (CoV 0.1097 vs. 0.1147; Brown-Forsythe p<0.001).
**Impact on Later Analysis:** Notebook 05's business language reflects both the statistical result and its practical size explicitly ("supported in the statistical sense, but the practical size of the difference is small") rather than treating a significant p-value alone as evidence of a large operational difference.
**Would This Decision Be Revisited?** Yes, if the business need shifts from "day-to-day total demand planning" to "SKU-level assortment risk," which would justify a different, explicitly-scoped volatility definition rather than replacing this one.

---

## Decision 07

**Decision ID:** D07
**Notebook:** 04 — Forecast Benchmark
**Business Question:** Before investing in advanced forecasting, how well does a simple, transparent baseline perform?
**Issue Observed:** No forecasting method of any kind existed prior to this project; demand planning relied entirely on manual judgement.
**Possible Options Considered:**
- ARIMA (classical time-series model, requires stationarity handling and per-series tuning).
- Prophet (component-based forecasting library, handles seasonality and holidays automatically).
- XGBoost or another gradient-boosted/ML model (flexible, but requires feature engineering, hyperparameter tuning, and ongoing retraining).
- Seasonal Historical Average (a simple mean of historical same-period values).
**Chosen Decision:** Seasonal Historical Average.
**Reason — why benchmark first:** none of the more sophisticated methods has a documented baseline to be measured against; without first establishing what a simple, zero-maintenance method achieves, any accuracy claimed for a more complex model cannot be shown to have earned its added cost and opacity.
**Reason — why not ARIMA:** requires per-SKU-store stationarity diagnosis and tuning (91 SKUs × 2 stores = 182 series), adding modeling overhead disproportionate to a first benchmark, and produces a forecast a planner cannot recompute by hand.
**Reason — why not Prophet:** a component-based model is a reasonable production candidate later, but introduces a dependency and a set of default assumptions (automatic seasonality/holiday detection) that would need to be validated — validation this project has not yet performed.
**Reason — why not XGBoost (or similar):** requires feature engineering, training/validation infrastructure, and retraining discipline that has not been justified by any demonstrated failure of a simpler method; per Notebook 04 Section 8, the observed forecast error is concentrated in one explainable segment (promotions), not a diffuse pattern an ML model would be uniquely suited to capture.
**Evidence Supporting the Decision:** Notebook 04, Sections 1–2.
**Impact on Later Analysis:** Sets the 28.63% out-of-sample WAPE figure as the benchmark every future forecasting investment must be measured against, per Notebook 04's "Final Requirements" (single source of truth for forecasting metrics).
**Would This Decision Be Revisited?** Yes — specifically once a targeted promotion-adjustment to this same benchmark has been tried and evaluated (per Notebook 05's recommendations); if that adjustment still leaves a diffuse, unexplained error pattern, a more sophisticated method would then have a documented gap to justify its cost.

---

## Decision 08

**Decision ID:** D08
**Notebook:** 04 — Forecast Benchmark
**Business Question:** How should forecast accuracy be measured and communicated?
**Issue Observed:** No single error metric answers every relevant question — a metric that is easy to communicate (like MAPE) can be mathematically unstable on a real retail dataset with near-zero-sales days.
**Possible Options Considered:**
- Report a single metric only (e.g., MAPE alone, since it is the most commonly requested by non-technical stakeholders).
- Report MAE, RMSE, MAPE, and WAPE together, each serving a distinct diagnostic purpose.
**Chosen Decision:** Report all four metrics together, with WAPE designated as the primary business metric.
**Reason:** MAE communicates typical error in plain dollar terms but is insensitive to a small number of very large misses; RMSE surfaces those large misses by penalizing them more heavily, at the cost of being harder to explain directly; MAPE is intuitive as a percentage but becomes unstable when actual sales are near zero (0.286% of test rows in this dataset), inflating the metric in a way that misrepresents overall accuracy; WAPE avoids that instability by aggregating total absolute error over total sales value, and is the metric this project relies on for business communication as a result.
**Evidence Supporting the Decision:** Notebook 04, Section 4 (MAE $119.88, RMSE $207.21, MAPE 54.18% excluding near-zero actuals, WAPE 28.63%).
**Impact on Later Analysis:** Every accuracy statement in Notebook 04 and Notebook 05 cites WAPE first, with the other three metrics available for a more technical reader, rather than any one metric being reported in isolation.
**Would This Decision Be Revisited?** No, unless the underlying sales data changes in a way that removes the near-zero-actual rows that make MAPE unreliable here.

---

## Decision 09

**Decision ID:** D09
**Notebook:** 05 — Business Recommendations
**Business Question:** What is the return on investment of the recommended actions?
**Issue Observed:** Every notebook in this project (01–04) confirmed that no holding-cost, capital-cost, or promotion-budget data exists anywhere in the dataset.
**Possible Options Considered:**
- Estimate ROI using an assumed or industry-typical cost figure, to give stakeholders a number to plan around.
- Estimate a rough range, caveated heavily.
- Do not estimate ROI at all; state explicitly that the data required does not exist.
**Chosen Decision:** Do not estimate ROI. State explicitly that cost data is absent.
**Reason — missing cost data:** any ROI or cost-savings figure would require a holding-cost rate, a capital-cost rate, or a promotion-spend figure, none of which appear anywhere in the source dataset (confirmed at the data-audit stage, Notebook 01, and reconfirmed at the recommendation stage, Notebook 05, Section 11).
**Reason — governance principle:** a project whose entire method rests on not overstating evidence cannot then produce a fabricated financial estimate merely because stakeholders would find a number more satisfying than its absence. An invented ROI figure would be indistinguishable, to a reader, from a genuinely evidenced one — which is precisely the failure mode this project's governance exists to prevent.
**Evidence Supporting the Decision:** Notebook 01 (data dictionary confirms no cost fields); Notebook 05, Section 8 ("No ROI figure, cost saving, or revenue impact is estimated here").
**Impact on Later Analysis:** Every business-impact statement in Notebook 05 is phrased qualitatively (planning focus, decision quality, transparency, efficiency) rather than in dollar terms.
**Would This Decision Be Revisited?** Yes, immediately, if holding-cost, capital-cost, or promotion-spend data becomes available — at that point, an ROI estimate would become a legitimate next analysis, not an invented one.

---

## Decision 10

**Decision ID:** D10
**Notebook:** 05 — Business Recommendations
**Business Question:** How should recommendations be prioritized and communicated?
**Issue Observed:** Several business ideas that could plausibly follow from the analysis (broad promotion expansion, immediate ML adoption, a SKU-store risk prioritization program, replacing planner judgement outright) are attractive on their face but are not equally supported by the evidence produced in Notebooks 03–04.
**Possible Options Considered:**
- Present all plausible ideas as recommendations, differentiated only by informal caveats in the text.
- Classify every recommendation by an explicit evidence-strength category (Strong, Moderate, Weak, Not Supported, Insufficient Data) and gate the recommendation language to that category.
**Chosen Decision:** Explicit evidence-strength classification, with a dedicated "Recommendations NOT Supported" section listing rejected ideas and the specific evidentiary reason for rejection.
**Reason:** An attractive business idea and a well-evidenced one are not the same thing, and presenting them with the same confidence of language would let the more appealing (but less supported) idea crowd out the better-evidenced one in a reader's attention. A explicit strength label, applied consistently, forces every recommendation to justify its own confidence level rather than borrowing credibility from the project's rigor elsewhere.
**Evidence Supporting the Decision:** Notebook 05, Sections 3, 7, 9, and 10 apply this classification consistently; Section 7 specifically documents why immediate ML adoption, forecasting software purchase, broad inventory increases, aggressive promotion expansion, and a SKU-store risk program were all rejected as recommendations at this stage, despite being plausible ideas.
**Impact on Later Analysis:** This is the final classification layer of the project — every recommendation surfaced in the README and Executive Summary traces back to a specific evidence-strength label assigned here, not to an unlabeled assertion.
**Would This Decision Be Revisited?** Yes, per-recommendation, as new evidence resolves each "Insufficient Data" or "Moderate" item (e.g., once planner-forecast logging exists, the forecasting-replacement recommendation can be re-evaluated on its own evidentiary merits).
