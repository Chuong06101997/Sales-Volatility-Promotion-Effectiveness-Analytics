# Sales Volatility, Promotion Effectiveness & Forecasting Benchmark Review

## 1. Executive Summary

Demand planning for this two-store FMCG retail operation has relied primarily on manual judgement, producing recurring stock-outs during peak demand periods and excess inventory during slower ones. This project examined three years of daily transaction data to determine whether the underlying sales patterns are predictable enough to justify a change in planning practice, before any investment in more sophisticated forecasting infrastructure.

The analysis proceeded through five stages — data audit, exploratory analysis, formal hypothesis testing, forecast benchmarking, and business recommendation — each building only on what the prior stage had confirmed. Four of six formally tested hypotheses were supported: promotions are associated with a measurable sales lift ($151.53 mean difference, Mann-Whitney p<0.001) with no detectable pull-forward dip in the three days following; weekends are a real, standalone driver (+$79.09 mean difference); and season affects sales, driven specifically by Summer (highest) and Spring (lowest). Two hypotheses were not supported: no evidence of a post-promotion dip was found, and weather (temperature, rainfall) showed no measurable effect on sales across every test applied. Store-level volatility differs in a way that is statistically detectable but practically modest (coefficient of variation of 0.1097 versus 0.1147 on daily total sales).

A transparent, non-machine-learning forecasting benchmark — the Seasonal Historical Average — was built on 2021–2022 data and evaluated out-of-sample against the full 2023 year, achieving 28.63% Weighted Absolute Percentage Error (WAPE) overall. Forecast error is concentrated almost entirely in promotion days (39.25% WAPE, versus 27.31% on non-promotion days), rather than spread unexplainably across stores, categories, or holidays. On this evidence, immediate investment in machine learning forecasting is not supported.

Three recommendations follow directly from this evidence: apply a manual adjustment to promotion-day forecasts, where the benchmark's error is concentrated; pilot a controlled, margin-tracked increase in promotion frequency; and run the forecasting benchmark in parallel with current planner judgement, rather than replacing it, since no comparison data currently exists to justify that replacement. No recommendation in this report involves a system purchase, an organizational change, or an estimated financial return — the dataset does not contain the cost data required to support a return-on-investment figure, and none is estimated.

## 2. Business Context

The business operates two retail locations — one in Berlin, Germany (STORE0001, a hypermarket) and one in Rome, Italy (STORE0002, a supermarket) — selling 91 SKUs across five product categories. Three years of daily transaction history (January 2021 through December 2023) were available for analysis.

Prior to this project, no formal statistical testing had been applied to confirm which patterns Supply Chain and Commercial teams had assumed to be true: whether sales genuinely follow a seasonal pattern, whether promotions produce real incremental lift or simply shift the timing of sales that would have occurred anyway, and which products or periods carry the greatest planning risk. Demand planning decisions — inventory buffers, promotion timing, and replenishment quantities — were being made without that evidence base.

A structural characteristic of the dataset was identified at the outset and carried through every subsequent stage of analysis: store, country, city, and sales channel are perfectly confounded in this dataset — each store maps to exactly one country, one city, and one channel, with no exception across all 152,460 transaction records (Notebook 01, Store Validation). This means any comparison between the two stores is a comparison between these two specific business units only, and cannot be generalized to "hypermarkets versus supermarkets" or to country- or channel-level conclusions. This constraint governs the scope of every store-related finding in this report.

## 3. Business Objectives

The project was scoped to answer eight specific business questions, established before any statistical testing began and tested without exception in Notebook 03:

#	Business Question
BQ1	Do promotions increase sales?
BQ2	Do sales fall immediately after promotions end (a post-promotion dip)?
BQ3	Do weekend sales differ from weekday sales?
BQ4	Does season affect sales?
BQ5	Does weather affect sales?
BQ6	Does sales volatility differ between stores?
BQ7	Is a simple, transparent forecasting benchmark accurate enough to be useful before considering more advanced methods?
BQ8	What should Supply Chain and Commercial teams do based on the evidence produced by BQ1–BQ7?

The objective throughout was not to maximize statistical sophistication or forecasting accuracy for its own sake, but to establish which business decisions the data can currently support with evidence, which require further validation, and which should not yet be made at all. Every recommendation in this report is scoped to that standard: no recommendation is permitted to claim more confidence than the evidence behind it.

## 4. Dataset Overview

The dataset comprises 152,460 daily transaction records spanning January 1, 2021 through December 31, 2023 (1,095 calendar days, with no missing calendar days across the full period) across 2 stores, 91 SKUs, 5 product categories, and 17 subcategories (Notebook 01, Sections 3, 7, 9).

Structural composition:

Store	Country	City	Channel	Transactions	Unique Products	Total Net Sales
STORE0001	Germany	Berlin	Hypermarket	87,600	80	$41,939,805.83
STORE0002	Italy	Rome	Supermarket	64,860	60	$21,903,478.75

Data quality, established in Notebook 01:

One row (0.0007% of the dataset) contained ten missing commercial and supply fields simultaneously, coinciding with a product's final recorded day of activity at one store; this row was removed rather than imputed and is excluded from every subsequent notebook (Decision Log, D01).
No exact duplicate records were found.
No invalid values were found across units sold, prices, discounts, net sales, stock levels, or lead times.
Revenue fields are internally consistent: gross sales equals units sold multiplied by list price exactly, and net sales equals gross sales adjusted for the recorded discount to within half a cent in every row.
Promotion coverage stands at 8.33% of all SKU-store-days, with the promotion flag and discount percentage fields fully consistent with each other (zero contradictions in either direction).
A small number of extreme values were identified in units sold (0.42% of rows) and net sales (1.01% of rows) using a deliberately wide statistical threshold; these were documented, not removed, since the majority coincide with promotional activity rather than representing data errors.

No customer-level data, competitor data, or cost data (holding cost, capital cost, or promotion spend) exists anywhere in the dataset — a constraint that directly shapes the scope of the business recommendations in Section 9 and is discussed further in Section 11.

## 5. Methodology

The analysis followed five sequential stages, each gated on the confirmed output of the one before it, matching the five-notebook structure of the project.

Stage 1 — Data Audit (Notebook 01). Before any pattern was explored, the dataset was checked for missing values, duplicates, data type correctness, date integrity, structural consistency (store, product, promotion fields), numeric validity, and internal revenue-field consistency. This stage exists on the principle that a finding built on unverified data is not a weak finding but a wrong one, indistinguishable from a correct one until it fails in practice.

Stage 2 — Exploratory Data Analysis (Notebook 02). Using the audited dataset, distributions, time trends, seasonal patterns, promotion comparisons, product and store contributions, weather relationships, and simple correlations were examined descriptively. This stage generated candidate hypotheses only; no test of statistical significance, and no conclusion, was drawn at this stage.

Stage 3 — Formal Hypothesis Testing (Notebook 03). Every candidate pattern identified in Stage 2 was tested formally, using one statistical method selected in advance per business question rather than multiple competing methods per question. Test selection followed directly from the data's characteristics: net sales are strongly right-skewed (skew ≈ 1.9–2.0) and fail a normality check even on a reduced sample, so non-parametric methods were used throughout — Mann-Whitney U for two-group comparisons (promotion effect, post-promotion effect, weekend effect), Kruskal-Wallis with Dunn's post-hoc test for the four-group seasonal comparison, and Brown-Forsythe for the store-volatility variance comparison. This is the only notebook in the project that performs statistical testing, and it is treated as the sole statistical source of truth: no test is repeated, re-derived, or re-interpreted elsewhere in the project.

Stage 4 — Forecast Benchmark (Notebook 04). A deliberately simple, transparent forecasting method — the Seasonal Historical Average — was constructed and evaluated on a genuine chronological out-of-sample split (trained on 2021–2022, evaluated against 2023), rather than an in-sample or randomly split evaluation. The objective of this stage was explicitly not to maximize forecasting accuracy, but to establish a trustworthy, explainable baseline against which any future, more complex method would need to demonstrate its value.

Stage 5 — Business Recommendations (Notebook 05). Findings from Stages 3 and 4 were converted into prioritized business actions, with every recommendation labeled by evidence strength and explicitly scoped to what the data supports. No new statistical test, forecast, or metric was introduced at this stage.

Throughout all five stages, every analytical decision — what to exclude, which test to use, which definition of a metric to adopt as canonical — was recorded at the time it was made in the project's Decision Log, and every assumption the analysis depends on is recorded separately in the Assumption Register.

## 6. Exploratory Data Analysis

Notebook 02 examined the audited dataset descriptively, to identify which patterns were worth the cost of formal statistical testing in Notebook 03. No statistical test, p-value, or significance claim appears in this stage; every observation below is a visual or descriptive impression only, framed in the notebook using language such as "appears" or "is consistent with" rather than "proves" or "confirms."

Sales distribution. Both net sales and units sold appear right-skewed rather than symmetric, with a visible concentration of high-value observations in the upper tail of each distribution — a pattern later confirmed formally via skew and kurtosis diagnostics in Notebook 03, and the basis for selecting non-parametric statistical methods at the testing stage.

Sales over time. The daily sales trend, smoothed with 7-day and 30-day rolling averages, does not appear flat across the three-year window. A recurring elevated period is visually apparent around the middle of each year, consistent with a seasonal pattern worth testing formally.

Seasonality. Monthly boxplots and average sales both show a visibly elevated period spanning roughly June through August, with a lower plateau across the spring months. Weekday averages appear relatively flat from Monday through Friday, with Saturday and Sunday visibly higher — the basis for the weekend and seasonal hypotheses subsequently tested in Notebook 03.

Promotion exploration. Promotion-day sales appear shifted higher than non-promotion-day sales in both the distribution histogram and the boxplot comparison, with both the average ($557.66 vs. $406.13) and median ($369.60 vs. $260.80) higher on promotion days (Notebook 02, Section 5). These are the same figures independently confirmed via formal testing in Notebook 03, Section 3.

Product exploration. Revenue is not evenly distributed across all 91 SKUs: 43 of the 91 SKUs account for 80% of total revenue, a moderate concentration rather than an extreme one. Category-level revenue contribution appears reasonably balanced rather than dominated by a single category.

Store exploration. STORE0001 shows higher total revenue and a higher average daily sales figure than STORE0002, consistent with its larger product assortment (80 SKUs versus 60). The coefficient of variation of daily total sales appears similar between the two stores (0.11 for both, at two-decimal precision) in this exploratory pass — the more precise figures (0.1097 versus 0.1147) and the formal test of whether this difference is statistically detectable are established in Notebook 03, Section 8.

Weather exploration. Scatterplots of net sales against temperature and against rainfall both show a flat, uncorrelated-looking pattern, with simple correlation coefficients close to zero for both variables (-0.0038 for temperature, 0.0002 for rainfall). This descriptive null result is the basis for the formal weather hypothesis subsequently tested in Notebook 03, Section 7.

Correlation exploration. Among the numeric variables examined, units sold shows a strong positive correlation with net sales (a mechanical relationship, not an insight), while list price, discount percentage, and promotion status show weaker positive correlations. Temperature, rainfall, holiday status, and stock-out status all sit close to zero. No causal interpretation was drawn from any correlation at this stage — each correlation served only to shortlist a variable for formal testing.

Candidate drivers and hypotheses. Based on this exploration, promotion status, weekend status, season, store, category, and price were identified as candidates for formal testing, while weather was identified as a candidate for a formal null-result test given its consistently flat relationship with sales. These candidates map directly to the six hypotheses (H1–H6) formally tested in Notebook 03, Section 7 of this report.
