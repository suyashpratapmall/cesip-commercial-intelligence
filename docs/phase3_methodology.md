# CESIP — Phase 3: Decision Science Layer
**Statistical Modeling, Hypothesis Testing & Segmentation**

---

## What This Phase Does

Phase 3 applies formal statistical methods to move from *describing* what happened in India's payments market to *explaining* and *predicting* it. Four modules are implemented.

---

## Module 3.1 — OLS Regression: What Predicts CC Spend Growth?

**Model:** `CC_POS_YoY_Growth = β₀ + β₁(Repo%) + β₂(CPI%) + β₃(GST_YoY_Growth) + β₄(Travel_Intensity_Index) + ε`

**Results (n=89 months, R²=0.452, F=17.29, p<0.001):**

| Predictor | Coefficient | p-value | Significance |
|---|---|---|---|
| CPI% (inflation) | -7.70 | < 0.001 | *** Significant |
| GST YoY Growth | +0.49 | < 0.001 | *** Significant |
| Repo Rate | -3.93 | 0.395 | Not significant |
| Travel Intensity | +0.07 | 0.602 | Not significant |

The model explains 45.2% of variance in CC spend growth — meaningful for a monthly macro model without firm-level data.

**Key findings:**
- **CPI is the strongest suppressant.** Every 1 percentage point rise in inflation reduces CC YoY growth by 7.7pp. High inflation erodes consumer purchasing power and suppresses discretionary spend.
- **GST growth is the strongest positive driver.** Every 1pp rise in GST collection growth (a real economic activity proxy) adds 0.49pp to CC growth. This relationship is highly significant and robust.
- **Repo Rate is in the expected negative direction but not independently significant.** Rate hikes suppress CC growth indirectly — through their effect on inflation and economic activity — rather than as a direct first-order effect. This is the nuanced finding: monetary policy works through channels, not in isolation.
- **Travel Intensity is not independently significant** due to multicollinearity with economic activity variables. Its effect on premium spend is real but captured through the broader economic signal.

---

## Module 3.2 — Three Hypothesis Tests

### Test 1: Demonetization Impact on CC/DC Ratio
- **H₀:** CC/DC ratio unchanged before vs after demonetization
- **Pre-demo mean (Jan–Oct 2016):** 1.513 | **Post-demo mean (Apr–Dec 2017):** 0.987
- **Result: REJECT H₀** (p < 0.0001)
- **Interpretation:** Demonetization caused a statistically significant and sharp DROP in credit card preference. The cash shortage of Nov 2016 paradoxically hurt CC spend more than DC spend — consumers reverted to debit and cash for essential transactions. However, the structural upward trend resumed, confirming this was a transient shock, not a permanent reversal.

### Test 2: Rate Tightening and CC Growth
- **H₀:** CC YoY growth equal in Normal Growth vs Rate Tightening regimes
- **Normal mean:** 20.6% | **Rate Tightening mean:** 10.0% | **Gap:** -10.6pp
- **Result: FAIL TO REJECT H₀** (p = 0.081)
- **Interpretation:** The 10.6pp gap is economically meaningful but not statistically significant at 5% level. High variance within the Normal Growth regime (which includes both pre-COVID high-growth months and some COVID-affected periods) absorbs the signal. Business reading: rate hikes moderate CC growth but do not halt it — the market is resilient to rate cycles.

### Test 3: COVID Recovery vs Pre-COVID Normal
- **H₀:** COVID Recovery growth = pre-COVID normal growth
- **Pre-COVID mean (2017–2019):** 34.2% | **Recovery mean:** 54.1% | **Gap:** +19.9pp
- **Result: REJECT H₀** (p = 0.017)
- **Interpretation:** The recovery was a structural acceleration, not a mean reversion. Suppressed demand, combined with accelerated digital adoption and changed consumer behavior, created a step-change in CC spend intensity. This finding validates the hypothesis that COVID was a structural inflection point, not just a temporary disruption.

---

## Module 3.3 — K-Means Segmentation: Four Spend Archetypes

K-Means clustering (k=4, validated by elbow method) on six standardized variables (CC spend, DC spend, CC/DC ratio, Travel Intensity, Repo Rate, CPI) identifies four distinct spend environment archetypes:

| Cluster | Label | CC POS (avg) | CC/DC Ratio | Travel Index | Dominant Regimes |
|---|---|---|---|---|---|
| A | Early Adoption Era | Rs46,417 Cr | 0.97x | 92.3 | Normal Growth (early years) |
| B | Disruption Period | Rs62,084 Cr | 1.10x | 55.3 | COVID Lockdown + Recovery |
| C | Maturing Growth | Rs41,599 Cr | 1.42x | 92.3 | Normal + Rate Tightening (mid-period) |
| D | Premium Expansion | Rs68,574 Cr | 2.60x | 112.3 | Rate Tightening (2023–2025) |

**AmEx strategic implication:** Cluster D (Premium Expansion) represents the current environment — high CC spend, strong credit preference, and above-baseline travel. This is the most favorable environment for AmEx's premium card products. Product strategy, credit limit expansion, and retention investment should be calibrated for this cluster.

---

## Module 3.4 — Trend Forecasting

Two linear trend models are estimated:

**Full period model (2016–2025):** Slope of Rs329 Crore per month. R²=0.44. Projects monthly CC POS spend reaching Rs72,600–76,200 Crore range through 2026.

**Post-2022 model (more recent trajectory):** Slope is steeper — the recent trend is growing faster than the full-period average. This is consistent with the Premium Expansion cluster becoming dominant post-2022.

**Limitation:** Linear trend does not capture seasonality (October spikes) or potential macro shocks. Forecasts represent a directional baseline, not a precision estimate.

---

## Charts Produced

- `01_ols_regression` — Coefficient plot with significance stars, actual vs fitted scatter, residual plot
- `02_hypothesis_tests` — Three box plots with p-value bridges and significance annotations
- `03_segmentation` — Elbow curve (k selection), cluster scatter on CC Spend vs CC/DC Ratio, timeline of cluster assignment
- `04_forecasting` — Full-period trend with forecast bands; post-2022 trend with recent-trajectory forecast

---

*Analytical code and raw data maintained in private repository.*
