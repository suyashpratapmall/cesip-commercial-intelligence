# CESIP — Phase 4: Risk & Governance Layer
**Anomaly Detection, Stress Testing & Operational Governance**

---

## What This Phase Does

Phase 4 builds the risk and governance layer — the component that makes CESIP an enterprise-grade platform rather than an analytics exercise. Four modules are implemented across two dimensions: **risk detection** (what went wrong or unusual) and **governance** (how well the system is controlled and documented).

---

## Module 4.1 — Anomaly Detection

Two methods are applied in parallel on CC POS spend data.

**Z-Score method (parametric):** Flags months where CC POS value or YoY growth rate exceeds ±2 standard deviations from the mean. Assumes approximate normality, which holds for this dataset.

**IQR method (non-parametric):** Flags months outside [Q1 − 1.5×IQR, Q3 + 1.5×IQR]. More robust to skew and extreme outliers; does not assume distributional form.

**Results:**

| Month | Variable | Z-Score | Event Explanation | Risk Classification |
|---|---|---|---|---|
| Oct 2021 | CC POS Level | +2.81 | Post-lockdown festival surge (Diwali 2021) | Explained — genuine demand |
| Nov 2021 | CC POS Level | +2.12 | Continuation of recovery surge | Explained — genuine demand |
| Dec 2021 | CC POS Level | +2.39 | Year-end spending peak | Explained — seasonality |
| Jan 2022 | CC POS Level | +2.04 | Elevated post-festival baseline | Explained — structural shift |
| Oct 2025 | CC POS Level | +2.07 | New structural high | Explained — market growth |
| Apr 2020 | YoY Growth | −2.46 | COVID lockdown collapse | Explained — policy event |
| Apr 2021 | YoY Growth | +4.95 | Base effect (Apr 2020 = near-zero) | Explained — mathematical artifact |
| Oct–Nov 2022 | YoY Growth | −2.0/−2.1 | YoY comparison vs 2021 festival peak | Explained — base effect |

**Governance verdict: Zero unexplained anomalies across 119 months.** All 9 flagged observations have documented, business-rational explanations. This is the expected outcome of a well-governed dataset — anomalies should be explainable, not suppressed.

---

## Module 4.2 — Macro Stress Testing

Using the OLS coefficients calibrated in Phase 3, seven stress scenarios are simulated to quantify CC spend growth resilience under adverse macro conditions.

**Baseline:** Recent 12-month average CC YoY growth = 14.9%

| Scenario | Shock | Projected CC Growth | Risk Level |
|---|---|---|---|
| Baseline | None | 14.9% | Low |
| Mild Rate Hike | Repo +100bps | 10.9% | Low |
| Severe Rate Hike | Repo +200bps | 7.0% | Medium |
| Inflation Spike | CPI +3pp | −8.2% | High |
| Growth Slowdown | GST YoY −10pp | 10.0% | Low |
| Combined Stress | Repo+200bps + CPI+3pp + GST−10pp | −21.0% | High |
| Favourable | Rate cut + CPI fall + GST growth | 29.0% | Low |

**Key findings:**
- A standalone rate shock (even +200bps) only reduces growth to 7% — still positive. The market is resilient to rate cycles.
- CPI is the most dangerous single variable. A 3pp inflation spike alone pushes CC growth negative (−8.2%). Inflation erodes real purchasing power faster than rates erode credit demand.
- The combined stress scenario (−21%) requires all three adverse conditions simultaneously — an extreme tail event, not a base case.
- The sensitivity chart confirms CPI has the steepest slope of all three variables — it is the dominant risk factor for CC spend.

**AmEx implication:** India's CC market is durable under monetary tightening but vulnerable to sustained inflation shocks. Product pricing and credit limit strategies should be more sensitive to CPI trajectory than to RBI rate decisions.

---

## Module 4.3 — GST Compliance Monitoring

GST collections are monitored as an independent proxy for economic activity. The GST/CC POS ratio is tracked with ±2σ control bands to identify months where fiscal collections and card spending diverge unusually.

**Result:** One flag in 108 months — April 2022 (z = 2.64).

April 2022 coincides with the fiscal year-end reconciliation period, when businesses file outstanding GST returns for FY2021-22. This caused a temporary spike in reported GST collections independent of actual transaction volumes. April is also the seasonal low for CC spend (seasonality index 87 from Phase 2). The combination of elevated GST and depressed CC spend produced the ratio spike.

**Governance verdict:** Not a compliance concern. The flag is explained by the mechanical interaction of fiscal year-end reporting and seasonal spend patterns.

**Directional alignment:** GST YoY growth and CC YoY growth move in the same direction in 76% of months (correlation r = 0.56). The 24% divergence months are concentrated in COVID and transition periods — structurally explainable.

---

## Module 4.4 — Operational Governance Dashboard

| Governance Metric | Value |
|---|---|
| Total months analysed | 119 |
| Payment data completeness | 100% |
| Documented null decisions | 7 |
| Anomaly months flagged | 9 |
| Anomalies explained | 9/9 (100%) |
| Unexplained outliers | 0 |
| GST compliance flags | 1/108 (0.9%) |
| OLS model R² | 0.452 |
| Model F-statistic p-value | < 0.001 |
| Hypothesis tests run | 3 |
| H₀ rejected | 2/3 |
| Overall risk rating | **LOW** |

**Volatility by regime (CC MoM Std Dev):** COVID Lockdown was the most volatile (28.0%), Rate Tightening the most stable (9.8%) — confirming that monetary policy regimes, while suppressing growth, also reduce spend volatility.

---

## Charts Produced

- `01_anomaly_detection` — CC POS with ±2σ bands and IQR upper bound; YoY growth Z-score bar chart with flagged months annotated
- `02_stress_testing` — Scenario bar chart with risk colour coding; sensitivity lines showing each variable's per-pp impact
- `03_gst_compliance` — GST/CC ratio control chart with compliance band; GST vs CC growth directional alignment scatter
- `04_governance_dashboard` — 6-panel dashboard: data completeness, anomaly counts by regime, model KPIs, volatility, stress results, rolling volatility

---

*Analytical code and raw data maintained in private repository.*
