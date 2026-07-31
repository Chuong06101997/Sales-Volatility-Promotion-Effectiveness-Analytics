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

<img width="918" height="197" alt="image" src="https://github.com/user-attachments/assets/4a520fdc-af0e-4cee-bf81-6b391ed71a28" />


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

## 7. Statistical Findings

Notebook 03 is the sole statistical source of truth for this project. Six hypotheses were tested, each using one pre-specified primary method, with effect size and confidence level reported alongside every p-value so that statistical significance is never presented without a corresponding measure of practical magnitude.

H1 — Do promotions increase sales? Supported.
Promotion days show a mean net sales value of $557.66 versus $406.13 on non-promotion days (median $369.60 versus $260.80) — a mean difference of $151.53. A Mann-Whitney U test confirms this difference is not attributable to chance (p<0.001), with a rank-biserial effect size of -0.206, indicating a small-to-moderate effect. Promotions are associated with a genuine, measurable sales lift.

H2 — Do sales fall immediately after promotions end? Not supported.
For each promotion event, the average net sales across the three days immediately following was compared against that same SKU-store-month's normal (non-promotion, non-stock-out) baseline level. The average post-promotion sales figure ($377.63) was statistically indistinguishable from the baseline level ($376.13) — a difference of $1.50, tested via Mann-Whitney U (p=0.4255). No evidence of a post-promotion dip was found in this three-day window, which argues against a simple demand-shifting (pull-forward) explanation for the promotion lift identified in H1.

H3 — Do weekend sales differ from weekday sales? Supported.
Weekend sales average $475.17 versus $396.08 on weekdays (median $310.08 versus $256.77) — a mean difference of $79.09. A Mann-Whitney U test confirms this difference is not attributable to chance (p<0.001). Weekend is a real, standalone calendar effect.

H4 — Does season affect sales? Supported, with one qualification.
A Kruskal-Wallis test across the four seasons found a statistically detectable difference (H=206.59, p<0.001). Summer shows the highest average sales ($442.77) and Spring the lowest ($399.14), with Autumn ($413.82) and Winter ($419.25) in between. Dunn's post-hoc test (Bonferroni-corrected across six pairwise comparisons) found that Summer and Spring each differ significantly from every other season; the one pairing that does not reach significance is Autumn versus Winter. The seasonal effect is therefore supported, but specifically driven by Summer and Spring rather than by all four seasons being mutually distinct.

H5 — Does weather affect sales? Not supported.
Temperature (tested via Kruskal-Wallis across four quartile-based categories) showed no statistically detectable association with sales (p=0.325), with a negligible effect size (eta-squared ≈ 0.000003). Rainfall (tested via Mann-Whitney, dry versus rainy days) likewise showed no detectable association (p=0.665). This is stated narrowly, as no measurable effect found using these two weather measures on same-day data — not as a general claim that weather can never affect sales.

H6 — Does sales volatility differ between stores? Supported statistically, modest in practice.
Volatility was defined, as the single canonical measure for this comparison, as the coefficient of variation of daily total sales (net sales aggregated across all SKUs, per store, per day) — not transaction-level variance or any other alternative definition, following the reasoning documented in Decision Log entry D06. STORE0001 shows a coefficient of variation of 0.1097 and STORE0002 a coefficient of variation of 0.1147. A Brown-Forsythe test confirms these two daily-total-sales series have statistically different variances (p<0.001); however, given the large sample size underlying this test (1,095 daily observations per store), the practical size of the CoV gap itself is small. This finding should be communicated as "detectable but modest," not as evidence that the two stores behave very differently on a day-to-day basis. As with every store-level finding in this project, this result describes STORE0001 and STORE0002 specifically and does not generalize to store format, country, or channel more broadly.

Summary of evidence:

<img width="946" height="473" alt="image" src="https://github.com/user-attachments/assets/e8160fb4-f715-4e5d-ba6a-98dfe4829f62" />


## 8. Forecast Benchmark

Notebook 04 addressed a single question: before investing in more advanced forecasting methods, does a simple, transparent baseline already provide useful planning accuracy? This notebook is the sole canonical source for every forecasting metric in this project.

Method. The Seasonal Historical Average was selected deliberately as the simplest available benchmark: for a given SKU at a given store, the forecast for any calendar month is the average of that SKU's historical sales at that store in the same calendar month, computed only from non-promotion, non-stock-out days. Where a SKU-store combination had fewer than ten qualifying historical observations for a given month, the forecast fell back to a category-store-level average. This method requires no specialized software and can be recomputed and verified by a planner directly.

Evaluation design. The benchmark was trained exclusively on 2021–2022 data (101,724 observations) and evaluated against the full 2023 year (50,735 observations) as a genuine out-of-sample test — no information from the test period informed the forecast in any way, matching the condition a planner actually faces when forecasting a future they cannot yet observe.

Overall accuracy:

<img width="921" height="335" alt="image" src="https://github.com/user-attachments/assets/f0df1c11-42f8-47c5-8c2f-a41935ca795a" />


WAPE is treated as the primary business metric, since MAPE becomes mathematically unstable on the small share of rows (0.286%) with near-zero actual sales, while WAPE aggregates total absolute error over total sales value and avoids that distortion. A WAPE of 28.63% indicates that, across a full year the benchmark never saw during its construction, total forecast error amounts to just over a quarter of total sales value. The overall bias of +$4.65 indicates a very slight tendency to under-forecast on average.

Diagnostics by segment:

<img width="983" height="477" alt="image" src="https://github.com/user-attachments/assets/27daa4dc-8a74-4472-abf7-d8f8d8942110" />


Forecast error is concentrated almost entirely in promotion days, which is expected given the method's deliberate exclusion of promotional history from its calculation — the benchmark is not designed to anticipate a promotion, and this is the one segment where it should not be trusted without manual adjustment. Store, category, and holiday status each make little difference to accuracy.

Should machine learning be considered now? Not on this evidence. The benchmark's error is concentrated in one identified, explainable segment (promotions) rather than spread unexplainably across the business. A targeted adjustment to this same simple benchmark — informed by the promotion-lift finding in H1 — is the evidence-supported next step; machine learning has not yet been shown to be necessary, and adopting it now would add cost, complexity, and reduced interpretability without a demonstrated gap that only it could close. Whether this benchmark is more or less accurate than current planner judgement remains unresolved — no historical record of planner forecast accuracy exists for that comparison, and this is stated in Notebook 04 as Insufficient Data rather than assumed in either direction.
## 9. Business Recommendations

Notebook 05 converts the evidence established in Notebooks 03 and 04 into business decisions. It introduces no new statistical test, no new forecasting metric, and no recommendation stronger than its supporting evidence.

Recommendation 1 — SKU-store risk prioritization: Insufficient Data, not issued.
A recommendation to focus S&OP attention on the highest-risk SKU-store combinations would require a risk score computed at the individual SKU-store level, combining volatility and stock-out incidence. This ranking does not exist in the canonical Notebook 03 or Notebook 04 — Notebook 03's volatility test (H6) operates at the whole-store level only, and Notebook 04's diagnostics report accuracy by store and category, not by individual SKU-store combination. Recommending action on a ranking that has not been computed would exceed the evidence available, so this recommendation is not issued. What the current evidence does support is a narrower conclusion: since H6 shows the two stores do not differ dramatically in volatility, and Notebook 04 shows store and category make little difference to forecast accuracy, a broad store-level or category-level prioritization is not well supported either. The specific next analytical step — a SKU-store-level composite risk score — is identified as a candidate for future work (Section 12).

Recommendation 2 — Promotion strategy: a controlled pilot, not a broad expansion.
H1 established a $151.53 mean sales lift on promotion days (p<0.001), and H2 found no evidence of a post-promotion dip in the three days following. Together, these support piloting a modest increase in promotion frequency on a limited set of products, with profit margin tracked throughout — margin was not evaluated in Notebooks 03 or 04, so this pilot is also how that gap is closed. This recommendation stops short of a broad expansion for two explicit reasons: the promotion-lift finding is an association, not a demonstrated cause, since no controlled experiment was possible with this data; and no cost or margin data existed prior to this pilot to confirm the lift is profitable, not merely larger in gross terms.

Recommendation 3 — Forecasting: use the benchmark for routine planning, run it in parallel for the rest.
The Seasonal Historical Average (Section 8) is recommended as a starting point for routine, non-promotional planning, given its 28.63% overall WAPE and 27.31% WAPE specifically on non-promotion days. It is not recommended as a replacement for planner judgement: Notebook 04 explicitly marks the comparison between benchmark accuracy and current planner accuracy as Insufficient Data, since no historical record of planner forecast accuracy exists. The recommended action is to run the benchmark alongside current planning, and begin logging planner forecasts prospectively, so that comparison can finally be made on evidence rather than assumption. Machine learning adoption is explicitly not recommended at this stage, for the reasons established in Section 8.

Recommendations explicitly not supported, and why:
<img width="951" height="581" alt="image" src="https://github.com/user-attachments/assets/7899556a-a870-4358-bf89-18cfa14a392d" />

Business impact, stated qualitatively. The recommendations above improve planning focus (calendar-aware buffer planning around weekends and Summer/Spring), promotion decision quality (moving from assumption to a tested lift-and-no-dip pattern), and forecast transparency (a benchmark planners can verify by hand). No return-on-investment figure, cost saving, or revenue impact is estimated anywhere in this report: Notebooks 01 through 04 confirm no holding-cost, capital-cost, or promotion-spend data exists in the source dataset, and inventing such a figure would exceed what any notebook in this project established.

## 10. Governance and Analytical Traceability

This project maintains three governance documents alongside the analytical notebooks, each answering a distinct question a reviewer might ask.

The Decision Log records every significant analytical choice and why it was made in preference to an alternative — for example, why the single incomplete record was removed rather than imputed (D01); why Mann-Whitney U was selected over an independent t-test for the promotion, weekend, and post-promotion comparisons, given the confirmed non-normality of the sales distribution (D02); why Kruskal-Wallis with Dunn's post-hoc replaced a one-way ANOVA for the seasonal comparison (D04); why store volatility is defined canonically as the coefficient of variation of daily aggregated sales, and why earlier, transaction-level definitions were discarded after being shown to be distorted by each store's differing product-price mix (D06); why the Seasonal Historical Average was chosen over ARIMA, Prophet, or a gradient-boosted model as the first forecasting benchmark (D07); and why ROI was intentionally not estimated, given the confirmed absence of cost data in the source dataset (D09).

The Assumption Register records the twelve conditions the analysis depends on holding true — including that the two stores are analyzed only as these two specific stores with no generalization beyond them (A06), that seasonality is assumed relatively stable across the three-year observation window pending a year-over-year check that has not yet been performed (A04), and that a "not supported" statistical result (the post-promotion dip, the weather effect) is not to be read as proof that no effect exists under any possible test (A12). Each assumption is rated by likelihood of failure, severity if it does fail, and current status — several, including the seasonal-stability and planner-comparison assumptions, are explicitly marked as requiring future validation rather than treated as settled.

The Risk Register records fourteen forward-looking risks that could weaken these conclusions over time, spanning store expansion, changes to promotion policy or consumer behavior, forecast benchmark drift as 2021–2022 training data ages, the eventual arrival of cost data that would require Recommendation 2's and 3's impact sections to be revisited, new products lacking sufficient forecast history, data quality degradation, unmeasured external confounders such as competitor activity or macroeconomic conditions, and — added specifically for this project's governance — the risk that stakeholder teams do not supply requested operational data on time, and the risk that a recommended pilot is delayed or shelved for budget or resourcing reasons unrelated to the strength of its evidence.

Together, these three documents allow any recommendation in Section 9, any statistical claim in Section 7, or any forecasting figure in Section 8 to be traced back to the specific choice that produced it, the assumption that choice depends on, and the risk that could someday require it to be revisited.
11. Project Limitations

Two stores only. Every store-related finding in this report — including the H6 volatility comparison — describes STORE0001 and STORE0002 specifically. Because store, country, city, and sales channel are structurally confounded 1:1 in this dataset (Section 2), none of these findings can be generalized to a store format, a country, or a channel in general. A future store, in a different location or format, is not covered by any conclusion in this report.

No cost data. No holding-cost, capital-cost, or promotion-spend field exists anywhere in the source dataset. As a direct consequence, no return-on-investment figure, inventory-savings estimate, or promotion-margin figure appears anywhere in this report — not because these questions are unimportant, but because the data required to answer them honestly does not exist. Recommendation 2 (Section 9) exists in part to begin closing this specific gap.

No customer-level data. Nothing in this project reflects individual customer behavior, loyalty, or repeat-purchase patterns. All findings operate at the aggregate SKU-store-day level, and no recommendation in this report should be read as a statement about customer segments or customer-level response.

No controlled experiment. The promotion-lift finding (H1) is an association between promotion status and sales level, not a demonstrated causal effect. No controlled experiment or natural comparison group was available to isolate promotions from other unmeasured factors — competitor activity, in-store execution, or broader market conditions — that could also explain the observed pattern. This report maintains associative language for this finding throughout, and Recommendation 2 is scoped as a controlled pilot specifically because causation has not been established.

Forecast benchmark, not a production system. Notebook 04 evaluated a simple, transparent baseline method, not a deployed, monitored, or continuously retrained forecasting system. Its 28.63% WAPE describes what this specific method achieved on a single out-of-sample test against 2023 data; it does not describe what a production forecasting system, once built, would achieve, and it does not establish whether this benchmark outperforms current planner judgement, since that comparison data has never been collected.

Seasonality assumed stable across three years. Both the H4 seasonal test and the forecasting benchmark treat 2021, 2022, and 2023 as comparable years for the same calendar period. No year-over-year stability check has been performed (Assumption Register, A04); if the business or market shifted materially within this window, that shift would be blurred into the pooled three-year average rather than surfaced separately.

Each of these limitations directly shaped which recommendations appear in Section 9 and which were explicitly withheld — they are constraints that bounded the analysis as it was conducted, not caveats appended afterward.

## 12. Future Work

The following analytical steps are identified as natural extensions of this project, distinct from the recommendations in Section 9, which are business actions rather than further analysis:

A SKU-store-level composite risk score, combining a volatility measure and stock-out incidence at the individual SKU-store grain, would close the specific evidence gap identified in Recommendation 1 and allow a genuinely evidenced prioritization recommendation to be made.
Prospective logging of planner forecasts, once begun per Recommendation 3, would for the first time allow the Seasonal Historical Average benchmark to be compared directly against current judgement-based planning — currently marked Insufficient Data throughout Notebooks 04 and 05.
A year-over-year seasonal stability check, testing whether the seasonal pattern identified in H4 held consistently across 2021, 2022, and 2023 individually, rather than only in the pooled three-year average.
A targeted promotion-day forecast adjustment, informed by the H1 lift estimate, layered onto the existing Seasonal Historical Average rather than replacing it — the specific, evidence-supported next step identified in Section 8 before any machine learning method is considered.
Incorporation of cost data, should holding-cost, capital-cost, or promotion-spend data become available, at which point the Business Impact section of Notebook 05 should be revisited directly rather than estimated retroactively.
Re-validation of the promotion and seasonal findings should promotion mechanics or broader market conditions change materially, per Risk Register entries R02 and R06, rather than treating the current findings as permanent.

None of these represent a deficiency in the current analysis — each is a next step that depends on data or process changes outside this project's original scope.

## 13. Conclusion

This project set out to determine whether a two-store FMCG retail operation's sales patterns were predictable enough, and its promotion activity effective enough, to justify a change in demand-planning practice — before any investment in more advanced forecasting. The evidence produced a measured answer rather than either a confirmation or a rejection of the business's prior assumptions.

Four of six formally tested hypotheses were supported: promotions are associated with a real sales lift with no detected pull-forward dip in the following three days, weekends are a genuine standalone driver, and season affects sales in a way specifically attributable to Summer and Spring. Two were not supported: no post-promotion dip was found, and weather showed no measurable effect across every test applied. Store-level volatility differs in a way that is statistically real but practically modest. A simple, fully transparent forecasting benchmark achieves 28.63% WAPE out-of-sample, with its error concentrated almost entirely in one identified, explainable segment — promotion days — rather than spread unpredictably across the business.

On this evidence, three actions are supported now: apply manual adjustment to promotion-day forecasts, where the benchmark's error is concentrated; pilot a controlled, margin-tracked increase in promotion frequency; and run the forecasting benchmark alongside current planning to finally generate the comparison data needed to judge it properly. Several plausible ideas — immediate machine learning adoption, broad promotion expansion, inventory increases across the board, and outright replacement of planner judgement — were considered and explicitly not recommended, because the current evidence does not support any of them yet.

The purpose of this project was not to maximize statistical sophistication, but to improve business decisions with evidence proportional to what the data could genuinely support. Every finding in this report is scoped to that standard, and every recommendation stops exactly where the evidence stops.
