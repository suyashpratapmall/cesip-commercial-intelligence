# CESIP — Phase 1 Methodology: Data Foundation

**Project:** Corporate Economic & Spend Intelligence Platform  
**Phase:** 1 of 4 — Data Audit, Master Build & Feature Engineering

---

## Overview

Phase 1 establishes the analytical foundation of CESIP. Before any modeling or insight generation, the six source datasets are audited, merged, and enriched into a single master analytical table that all subsequent phases consume. This is a deliberate data governance practice — not a step skipped to get to "the interesting parts."

---

## Source Data Architecture

CESIP integrates six distinct data streams across India's payments ecosystem, macroeconomic environment, and economic activity indicators spanning January 2016 to November 2025 (119 monthly observations).

The data streams represent three analytical layers:

**Payments layer** — RBI's payment system indicators capture credit card POS spend, debit card POS spend, online transaction values, and ATM withdrawals. This is the core behavioral signal in the platform.

**Macro layer** — RBI Repo Rate and MoSPI CPI inflation provide the monetary policy and price environment in which spending occurs. These variables contextualize spend trends and allow real (inflation-adjusted) analysis.

**Economic activity layer** — GST collections, NIFTY sector indices (Banking, IT, FMCG), and DGCA aviation passenger data serve as independent proxies for economic health, sectoral activity, and travel behavior respectively.

---

## Merge Strategy

The **calendar_months** sheet serves as the master spine — a clean 119-month sequence with no gaps. All other sheets are left-joined onto this spine using the `Month (YYYY-MM)` key. This ensures the master table always contains 119 rows regardless of coverage differences in any individual source sheet.

This approach mirrors how commercial intelligence platforms handle multi-source data — a single authoritative time dimension, with feature availability documented rather than silently discarded.

---

## Data Governance Decisions

Seven substantive data quality issues were identified during the audit. Each was resolved with a documented decision rather than a silent fix:

**Online payment nulls (2016–2022):** The RBI Payment System Indicators did not separately report online credit card and debit card transactions before March 2022. These 74 monthly nulls are structurally correct — the reporting category did not exist, not the transactions. They are retained as null and the columns are analyzed only from their availability date.

**GST pre-July 2017:** India's Goods and Services Tax was enacted on 1 July 2017. GST Gross is null for January 2016 and January–June 2017 — 7 months. These nulls reflect legislative reality and are retained accordingly.

**GST Net limited availability:** The government began disclosing net GST (after refunds) only from late 2023. Only 24 months of GST Net data are available; GST Gross is used as the primary fiscal variable.

**Aviation 2025-11:** The DGCA had not published November 2025 data at time of collection. The row contains all nulls and is dropped before merging — the analysis treats October 2025 as the aviation endpoint.

**NIFTY December 2025:** The NIFTY sheet contains one additional month (December 2025) not present in the calendar spine. This is excluded automatically by the left join and no manual action is required.

---

## Feature Engineering Rationale

Fourteen derived features were created. The design choices reflect commercial intelligence priorities rather than academic convention.

The **CC/DC Ratio** is central to AmEx-style market analysis. American Express competes exclusively in the credit card space; a rising CC/DC ratio indicates structural shift toward credit products — a favorable market signal. Monitoring this ratio over economic cycles (demonetization, COVID, rate tightening) reveals whether the shift is durable or cyclical.

The **Travel Intensity Index** (normalized to 2019 pre-COVID baseline = 100) quantifies travel behavior as a proxy for premium card demand. AmEx's core value proposition — airport lounges, travel credits, concierge services — is directly linked to travel frequency. A rising travel intensity index is a leading indicator of premium card engagement opportunity.

**Real CC Spend** adjusts for CPI inflation using January 2016 as the base period. Nominal growth in spend can be misleading in a high-CPI environment — real growth isolates genuine volume increase from price-level effects.

**Year-over-year growth rates** (rather than month-on-month) are the primary growth metric in the platform. YoY strips seasonal patterns that would otherwise dominate month-level analysis — December retail spikes, summer travel peaks, festival seasons.

The **Economic Regime Flag** categorizes each month into five labeled environments: Normal Growth, Demonetization (Nov 2016 – Mar 2017), COVID Lockdown (Mar–Aug 2020), COVID Recovery (Sep 2020 – Dec 2021), and Rate Tightening (2022 onwards, Repo ≥ 6%). This flag enables regime-conditional analysis in Phases 2–4 — asking not just "what happened" but "what happened in each macro environment."

---

## Output

The master analytical table (`master_analytical_table.csv`) contains 119 rows and 37 columns. It is the single source of truth for all subsequent analytical phases. Every column is documented in the data dictionary. Every null is explained. Every derived variable has a traceable formula.

This discipline — treating data preparation as a governance exercise, not a preprocessing chore — is what separates enterprise analytics from student projects.

---

*Next: Phase 2 — Commercial Spend Intelligence (spend behavior analysis, economic event annotation, sector correlations)*
