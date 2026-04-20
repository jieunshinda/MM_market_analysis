# MM_market_analysis

1. Project Overview
This project builds an end-to-end market sizing and patient flow model for Multiple Myeloma (MM) in Korea, covering the period 2026–2030. The model is designed to support commercial decision-making for oncology products—specifically quantifying the addressable patient population and revenue opportunity for DARZALEX (Daratumumab) and Bortezomib-based regimens under current HIRA (Health Insurance Review and Assessment Service) reimbursement policy.

2. Data Sources
   Three independent data sources were used to calibrate the model:

  ▸  NCC (National Cancer Center) Korea: Annual MM incidence data, 2010–2023 (n=14 data points). Used for CAGR derivation and incidence projection baseline. Source: NCC Cancer Statistics portal.
  ▸  HIRA (Health Insurance Review and Assessment Service): Reimbursement approval dates and criteria for each regimen (DVTd Feb 2025, DRd, VRd 2022, DVd Sep 2025). Prevalence data (2010–2023) used for out-of-sample validation. OOS validation error: +3.5%.
  ▸  IQVIA Oncology Dynamics 2024: Real-world 1L market share by regimen in Korea — VRd 58% of all 1L patients, DVTd 11% (quadruplet), VMP declining from 61% (2021) to ~5% (2024). Used to calibrate TE/TIE market share assumptions.

4. Methodology
The model is structured in three sequential phases:
  Phase 1 — Epidemiology Baseline
    ▸  Log-linear CAGR regression: Fitted log-linear trend to NCC incidence data (2010–2023). Selected 5.2% CAGR after excluding COVID-19 distortion year (2020: −4.6%; 2021: +15.4% rebound). 
  Phase 2 — Patient Flow Model
    ▸  TE / TIE cohort split: 30% Transplant-Eligible (TE), 70% Transplant-Ineligible (TIE) of treatment-eligible patients (95% of NGMM). Calibrated against IQVIA real-world data.
    ▸  HIRA reimbursement filtering: 1L regimens assigned per HIRA approval status: DVTd (TE 1L, 40%), VRd (both arms), DRd (TIE 1L, 20%), VMP (TIE 1L, declining). Rd excluded at 1L TIE (not reimbursed).
    ▸  Dara-exposed / Dara-naive routing: Key 2L branching logic: Dara-exposed patients (DVTd/DRd 1L) cannot receive Dara-based regimens at 2L. Dara-naive patients → eligible for 2L DVd (20% uptake, HIRA reimbursed Sep 2025). 3L bispecifics modeled at 2% penetration (approved, but not reimbursed).
  Phase 3 — Revenue Model
    ▸  Per-patient revenue: HIRA reimbursement pricing applied per regimen (full treatment course): DVTd KRW 52.1mm, DRd KRW 102.1mm, DVd KRW 82.3mm, VRd KRW 8.3–16.6mm. Segmented by BTZ (Bortezomib) and DARA (Daratumumab) components.
    ▸  Annual cohort projection: New patients entering each regimen per year × per-patient revenue × CAGR growth. Revenue segmented by regimen, drug class (DARA vs. BTZ), and patient population (TE/TIE). 

5. Key Findings
 
  ▸  Incidence growing at 5.2% CAGR: Korean MM diagnoses projected to reach ~3,000/year by 2030, driven by aging demographics. 
  ▸  DRd (TIE 1L) is the highest per-patient DARZALEX revenue driver: At KRW 102.1mm/patient, DRd generates significantly more revenue per patient than DVTd (KRW 43.8mm) due to longer dosing duration in TIE patients. The 70% TIE arm share amplifies this.
  ▸  2L DVd (Sep 2025 reimbursement) creates incremental J&J opportunity: At 20% uptake from the Dara-naive pool, DVd adds KRW ~2.4–2.9bn annually to J&J revenue. This is a direct beneficiary of the Sep 2025 HIRA approval—not captured in pre-2025 models.
  ▸  Dara-naive pool is the 2L DVd eligibility gate: VRd + VTd + Others (60% of TE 1L) and VRd + VMP + Others (80% of TIE 1L) constitute the DVd-eligible pool. Increasing VRd uptake in TIE 1L (65% in 2026) creates a large eligible population as patients relapse.

6. Tools & Technical Implementation

  ▸  Python (pandas): Data processing, CAGR calculation, patient flow model, revenue computation
  ▸  Python (matplotlib): All visualizations — patient flow tree (FancyBboxPatch, annotate), line chart with CI band, stacked bar charts
  ▸  Python (statsmodels): Log-linear OLS regression for CAGR derivation
  ▸  Data sources: NCC Cancer Statistics (public), HIRA open data (public), IQVIA Oncology Dynamics 2024 (secondary research)
