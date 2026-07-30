# Sales Volatility, Promotion Effectiveness & Forecasting Benchmark Review
Full Analytical Report
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
