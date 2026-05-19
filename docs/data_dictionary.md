# CESIP — Data Dictionary
**Project:** Corporate Economic & Spend Intelligence Platform  
**Version:** 1.0 | Phase 1  
**Maintained by:** Suyash Pratap Mall

---

## Source Datasets

| Sheet | Rows | Date Range | Source |
|---|---|---|---|
| `payments_rbi_monthly` | 119 | Jan 2016 – Nov 2025 | RBI Payment System Indicators |
| `macro_months` | 119 | Jan 2016 – Nov 2025 | RBI Monetary Policy / MoSPI CPI |
| `gst_monthly` | 108 | Jul 2017 – Nov 2025 | Ministry of Finance GST Collections |
| `nifty_monthly` | 120 | Jan 2016 – Dec 2025 | NSE India |
| `aviation_monthly` | 119 | Jan 2016 – Nov 2025 | DGCA Monthly Statistics |
| `calendar_months` | 119 | Jan 2016 – Nov 2025 | Derived master time spine |

---

## Master Analytical Table — Column Reference

### Time Identifiers

| Column | Type | Description |
|---|---|---|
| `Date` | datetime | First day of each month (for time-series operations) |
| `Month (YYYY-MM)` | string | Period key used for all joins |
| `Year` | int | Calendar year |
| `Month_Number` | int | Calendar month (1–12) |

---

### Payments (RBI) — Source Variables
*Unit: ₹ Crore. Source: RBI Payment System Indicators.*

| Column | Nulls | Description |
|---|---|---|
| `CC_POS_Value` | None | Credit card Point-of-Sale transaction value |
| `CC_Online_Value` | Jan 2016 – Feb 2022 | Credit card online transaction value. **Governance note:** RBI began separate online reporting from March 2022. Pre-2022 nulls are structurally correct. |
| `CC_Cash_Withdrawal_Value` | None | Credit card ATM/cash advance value |
| `DC_POS_Value` | None | Debit card Point-of-Sale transaction value |
| `DC_Online_Value` | Jan 2016 – Feb 2022 | Debit card online transaction value. Same governance note as CC_Online_Value. |
| `DC_Cash_Withdrawal_Value` | None | Debit card ATM withdrawal value |

---

### Macroeconomic Variables
*Source: RBI (Repo Rate), MoSPI (CPI).*

| Column | Nulls | Description |
|---|---|---|
| `Repo%` | None | RBI Repo Rate (%) — benchmark lending rate set by Monetary Policy Committee |
| `CPI%` | None | Consumer Price Index inflation rate (%) — all-India, combined |

---

### GST Collections
*Unit: ₹ Crore. Source: Ministry of Finance.*

| Column | Nulls | Description |
|---|---|---|
| `GST Gross` | Jul 2017 onwards | Gross GST collected before refunds. **Governance note:** GST enacted 1 July 2017. 2016 and Jan–Jun 2017 nulls are structurally correct. |
| `GST Net` | 2023-12 onwards only | Net GST after refunds. Limited availability — use GST Gross as primary variable. |

---

### NIFTY Sector Indices
*Unit: Index points (closing). Source: NSE India.*

| Column | Nulls | Description |
|---|---|---|
| `BANK NIFTY` | None | Banking sector index — proxy for financial services health |
| `IT NIFTY` | None | Information technology sector index |
| `FMCG NIFTY` | None | Fast-moving consumer goods sector index — proxy for mass consumption |

---

### Aviation (DGCA)
*Unit: Passenger count. Source: DGCA Monthly Statistics.*

| Column | Nulls | Description |
|---|---|---|
| `Domestic_Scheduled` | None (2025-11 dropped) | Scheduled domestic air passengers |
| `Domestic_Non_Schedule` | None (2025-11 dropped) | Non-scheduled (charter) domestic passengers |
| `International_Scheduled` | None (2025-11 dropped) | Scheduled international passengers at Indian airports |
| `International_Non_Scheduled` | None (2025-11 dropped) | Non-scheduled international passengers |

**Governance note:** 2025-11 row dropped — DGCA data not published at time of collection.

---

### Engineered Features

| Column | Formula | Business Rationale |
|---|---|---|
| `Total_CC_Spend` | `CC_POS + CC_Online` | Total credit card spend (online only available from Mar 2022) |
| `Total_DC_Spend` | `DC_POS + DC_Online` | Total debit card spend |
| `Total_Card_Spend` | `Total_CC + Total_DC` | Market-level card payment volume |
| `CC_DC_Ratio` | `CC_POS / DC_POS` | Credit preference indicator — rising ratio = growing credit card market |
| `CC_Digital_Share` | `CC_Online / (CC_POS + CC_Online)` | Online channel penetration for credit cards (Mar 2022+) |
| `ATM_to_POS_Ratio` | `DC_ATM / (DC_POS + CC_POS)` | Cash dependency — declining = digital ecosystem maturity |
| `Total_Domestic_Pax` | `Domestic_Scheduled + Non_Schedule` | Total domestic air traffic |
| `Total_International_Pax` | `International_Scheduled + Non_Schedule` | Total international air traffic |
| `Total_Aviation_Pax` | `Domestic + International` | Total air passengers — travel intensity proxy |
| `Travel_Intensity_Index` | `(Total_Pax / 2019_avg) × 100` | Index relative to 2019 (pre-COVID baseline = 100) |
| `Real_CC_POS_Value` | `CC_POS × (CPI_Jan2016 / CPI_t)` | Inflation-adjusted CC spend (Jan 2016 base) |
| `CC_POS_MoM_Growth` | `pct_change(1)` | Month-on-month CC spend growth (%) |
| `DC_POS_MoM_Growth` | `pct_change(1)` | Month-on-month DC spend growth (%) |
| `CC_POS_YoY_Growth` | `pct_change(12)` | Year-on-year CC spend growth (%) — seasonality-adjusted |
| `DC_POS_YoY_Growth` | `pct_change(12)` | Year-on-year DC spend growth (%) |
| `Aviation_YoY_Growth` | `pct_change(12)` | Year-on-year aviation passenger growth (%) |
| `CC_POS_3M_Rolling` | `rolling(3).mean()` | 3-month rolling average — noise-smoothed CC spend |
| `DC_POS_3M_Rolling` | `rolling(3).mean()` | 3-month rolling average — noise-smoothed DC spend |
| `Real_Interest_Rate` | `Repo% - CPI%` | Real cost of credit (Fisher approximation) |
| `Economic_Regime` | Rule-based flag | Labels each month: Normal_Growth / Demonetization / COVID_Lockdown / COVID_Recovery / Rate_Tightening |

---

## Data Governance Log

| # | Issue | Sheet | Resolution |
|---|---|---|---|
| 1 | `CC_Online_Value` null for 74 rows | payments | Retained as null — RBI reporting change, not missing data |
| 2 | `DC_Online_Value` null for 74 rows | payments | Same as above |
| 3 | `GST Gross` null for 7 rows (2016 + Jan–Jun 2017) | gst | Retained as null — GST did not exist pre-July 2017 |
| 4 | `GST Net` only 24 non-null rows | gst | Use GST Gross as primary variable; GST Net treated as supplementary |
| 5 | `2025-11` aviation row — all nulls | aviation | Row dropped — DGCA data unpublished |
| 6 | `2025-12` in NIFTY, not in calendar | nifty | Excluded via left join on calendar spine |
| 7 | Payment values magnitude ~50,000 | payments | Confirmed ₹ Crore denomination (RBI standard) |

---

*This dictionary is maintained in the public repository for transparency and reproducibility documentation. All analytical code and raw data are maintained in the private working repository.*
