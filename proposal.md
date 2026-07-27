# Proposal: Sales Volatility, Promotion Effectiveness & Forecasting Benchmark Review

**Prepared for:** Supply Chain & Commercial Leadership
**Prepared by:** Business Analytics (Data Analyst)
**Status:** Proposed, pre-analysis

---

## 1. Project Overview

Supply Chain has flagged recurring stock-outs during busy periods and excess inventory during slow ones. Nobody has formally checked whether this pattern is predictable or whether the business is reacting to noise. This proposal outlines a focused analysis of three years of daily sales data to find out — before any money is spent on a forecasting platform.

## 2. Business Context

Demand planning today runs mostly on judgement. That's not necessarily wrong — experienced planners catch things a spreadsheet won't — but it also means nobody can currently answer basic questions with evidence: Do sales actually follow a seasonal pattern, or does it just feel that way? Are promotions paying for themselves, or just moving the timing of sales that would have happened anyway? Which products need the closest attention right now?

Two stores are available for this analysis, spanning three years of daily transactions across roughly 90 SKUs. That's enough history to test real patterns, with one caveat worth flagging immediately: an early look at the data shows each store maps to a single country, city, and sales channel. That means any store-level comparison in this analysis will describe these two specific locations — not "channel type" or "market" in general. Better to say that now than to overstate it later.

## 3. Business Problem

Planning decisions — how much stock to hold, when to run a promotion, which products need closer attention — are currently made without a clear, tested view of what actually drives sales volatility. The risk of leaving this untested: continuing to invest planning effort based on habit rather than evidence, or worse, investing in a forecasting tool before knowing whether a simple approach would already help.

## 4. Project Objectives

- Establish whether sales volatility follows testable patterns (seasonal, promotional, product-driven) or is largely unpredictable.
- Determine whether current promotion activity is producing real, sustained lift.
- Identify which products and locations carry the most demand-planning risk today.
- Build a simple, transparent forecasting baseline to judge whether further investment in forecasting is justified.
- Produce recommendations that are only as strong as the evidence behind them — nothing recommended on a hunch.

## 5. Business Questions

| ID | Question |
|---|---|
| BQ1 | Do sales show clear seasonal patterns? |
| BQ2 | Does sales volatility differ meaningfully between the two stores? |
| BQ3 | Are promotions associated with real, sustained sales lift, or short-term pull-forward? |
| BQ4 | Is promotion timing well-aligned with underlying demand patterns? |
| BQ5 | Which factors (product, promotion, seasonality, location) are most associated with sales volatility? |
| BQ6 | Which products and locations carry the highest demand-planning risk? |
| BQ7 | Should promotion timing or frequency change, and what evidence would justify that? |
| BQ8 | What business impact can realistically be estimated from this analysis, given the data available? |

## 6. Scope

- Full 3-year daily transaction history, both stores, all available SKUs and categories.
- Internal factors only: calendar effects, promotions, weather, product characteristics, pricing.
- Descriptive analysis, formal statistical testing, and a simple forecasting benchmark.
- Business recommendations tied explicitly to the strength of the supporting evidence.

## 7. Out of Scope

- Any claim that findings generalize beyond these two specific stores.
- Machine learning or advanced forecasting models — a simple, transparent benchmark comes first; anything more complex is a separate, later decision.
- Customer-level or competitor data — not available in this dataset.
- Return-on-investment figures, unless the necessary cost data turns out to be available (to be confirmed during the data review stage).
- Causal claims about promotions — a controlled experiment isn't possible here, so any promotion finding will be reported as an association, not proof of cause and effect.

## 8. Proposed Analytical Methodology

The analysis will follow a staged approach, with each stage gated on the one before it:

1. **Data audit** — confirm the data is trustworthy before drawing any conclusion from it (missing values, duplicates, consistency checks).
2. **Exploration** — look for candidate patterns worth testing formally; no conclusions drawn at this stage.
3. **Formal hypothesis testing** — test the patterns that surfaced during exploration, using methods appropriate to how the data actually behaves (not assumed in advance).
4. **Driver analysis** — estimate which factors are most associated with volatility, without claiming to prove causation.
5. **Forecasting benchmark** — build a simple, explainable baseline first, before considering anything more advanced.
6. **Recommendations** — every recommendation tied to an explicit evidence strength (strong, moderate, weak, not supported, or insufficient data), so nothing is recommended more confidently than the data supports.

Every decision made along the way — what data to exclude, what method to use, what to assume — will be logged as it happens, not reconstructed afterward.

## 9. Expected Deliverables

- A documented data audit.
- A set of tested findings, each rated by evidence strength.
- A simple forecasting benchmark and its evaluation.
- A short set of business recommendations, scoped to what the evidence actually supports.
- A decision log and assumption register, so the reasoning behind the analysis is visible, not just the conclusions.

## 10. Risks and Assumptions

- **Only two stores are available.** Any store-level finding will be limited to these two locations and won't be presented as evidence about "channels" or "markets" more broadly.
- **Cost and inventory-holding data may not be available.** If so, ROI and inventory-savings questions will be marked as not answerable yet, rather than estimated loosely.
- **Promotion effects, if found, will likely only be measurable as an association.** Without a controlled experiment, causation can't be established from this data alone.
- **Data quality is unverified at proposal stage.** The first phase of work will confirm whether the dataset is complete and consistent enough to support the rest of the analysis; if not, scope may need to be revisited.

## 11. Success Criteria

This project will be considered successful if it produces:

- A clear answer, evidence-based, on whether sales volatility is predictable or largely noise.
- A tested (not assumed) view of whether promotions are working.
- A short, prioritized list of products/locations needing planning attention.
- A working forecasting benchmark, simple enough to explain to a planner without a statistics background.
- Recommendations that a Supply Chain or Commercial Manager could act on immediately, each labeled with how confident the business should be in it.

Success is not measured by the sophistication of the method used — a correct "we don't have enough evidence yet" is as valuable an outcome as a confirmed pattern.

## 12. Project Timeline

| Stage | Focus | Estimated Duration |
|---|---|---|
| 1 | Business understanding & data requirements | 2-3 days |
| 2 | Data audit | 3-4 days |
| 3 | Exploratory analysis | 3-4 days |
| 4 | Formal hypothesis testing | 4-5 days |
| 5 | Driver analysis | 2-3 days |
| 6 | Forecasting benchmark | 3-4 days |
| 7 | Recommendations, governance write-up, and review | 3-4 days |

Timeline assumes data is available in full at project start and no major data-quality issues are found during the audit stage.
