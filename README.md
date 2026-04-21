# Korean Multiple Myeloma Market Sizing & Patient Flow Model

An end-to-end patient flow and market sizing model for Multiple Myeloma (MM) in Korea, 
covering 2026–2030. Built to quantify the J&J-addressable patient population and 
commercial revenue opportunity for DARZALEX and bortezomib-based regimens under 
current HIRA reimbursement policy.

---

## Background

Multiple Myeloma is a blood cancer affecting plasma cells. In Korea, treatment eligibility 
and regimen selection are tightly governed by HIRA reimbursement criteria — meaning 
which drug a patient receives at each treatment line depends not just on clinical factors, 
but on what HIRA has approved and when.

This model works through that structure systematically: starting from raw incidence data, 
splitting patients into transplant-eligible (TE) and transplant-ineligible (TIE) cohorts, 
routing them through 1L–3L treatment lines based on HIRA approval status, and projecting 
the resulting revenue by regimen and drug class through 2030.

---

## Data Sources

| Source | What It Provided | Access |
|--------|-----------------|--------|
| NCC (National Cancer Center) Korea | Annual MM incidence, 2010–2023 (n=14) | Public |
| HIRA | Reimbursement approval dates and criteria per regimen; prevalence data 2010–2023 for OOS validation | Public |
| IQVIA Oncology Dynamics 2024 | Real-world 1L market share by regimen in Korea | Secondary research |

**Out-of-sample validation**: HIRA prevalence data (2010–2023) was used to validate the 
incidence projection. OOS error: +3.5%.

---

## Methodology

### Phase 1 — Epidemiology Baseline

Fitted a log-linear trend to NCC incidence data (2010–2023) to derive a forward-looking 
CAGR. The 2020 data point was excluded due to COVID-19 distortion (−4.6% that year, 
followed by a +15.4% rebound in 2021). Final CAGR: **5.2%**.

### Phase 2 — Patient Flow Model

<img width="2685" height="1785" alt="patient_flow" src="https://github.com/user-attachments/assets/954fbf02-6e04-4194-bb05-8cf151d103bf" />

**TE/TIE cohort split**  
95% of newly diagnosed MM patients (NGMM) are treatment-eligible. Of those:
- 30% Transplant-Eligible (TE)
- 70% Transplant-Ineligible (TIE)

**1L**
Calibrated against IQVIA real-world market share data.

**Dara-exposed / Dara-naive routing at 2L**  
The key branching logic: patients who received a Dara-based regimen at 1L 
(DVTd or DRd) cannot receive Dara again at 2L. This creates two distinct pools:

- **Dara-exposed** → ineligible for 2L DVd
- **Dara-naive** → eligible for 2L DVd (20% uptake assumed; HIRA-reimbursed Sep 2025)

**3L**
bispecifics (Tecvayli, Talvey), Carvykti — approved in Korea but not yet reimbursed.

### Phase 3 — Revenue Model

Per-patient revenue calculated using HIRA reimbursement pricing for a full treatment course:

| Regimen | KRW per patient |
|---------|----------------|
| DRd | 102.1mm |
| DVd | 82.3mm |
| DVTd | 52.1mm |
| VRd | 8.3–16.6mm |

Revenue is segmented by regimen, drug class (DARA vs. BTZ), and patient population 
(TE/TIE). Annual projections apply the 5.2% CAGR to new patient cohorts entering 
each regimen each year.

---

## Key Findings

- **Incidence growing at 5.2% CAGR**: Korean MM diagnoses projected to reach ~3,000/year 
  by 2030, driven by aging demographics.

- **DRd is the highest per-patient revenue driver**: At KRW 102.1mm/patient, DRd generates 
  roughly 2.3× more revenue per patient than DVTd (KRW 43.8mm) — a function of longer 
  dosing duration in TIE patients, amplified by the 70% TIE arm share.

- **2L DVd creates incremental opportunity post-Sep 2025**: At 20% uptake from the 
  Dara-naive pool, DVd adds KRW ~2.4–2.9bn annually. This window didn't exist in 
  pre-2025 models.

- **VRd uptake is the key upstream variable for DVd eligibility**: VRd + VTd + Others 
  account for 60% of TE 1L and 80% of TIE 1L — the entire Dara-naive pool that feeds 
  2L DVd. As VRd uptake grows in TIE 1L (projected 65% in 2026), the eligible population 
  grows with it.

---

## Tools

| Tool | Usage |
|------|-------|
| Python — pandas | Data processing, CAGR calculation, patient flow model, revenue computation |
| Python — matplotlib | Visualizations (patient flow tree, CAGR projection, stacked revenue bars) |
| Python — statsmodels | Log-linear OLS regression for CAGR derivation |
