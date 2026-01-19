# FuelEU Monitoring Prototype

This repository contains a prototype implementation for plausibility-based monitoring of ship-level fuel consumption and emission intensity under the EU FuelEU Maritime framework.

The project was developed as part of a logistics case challenge and focuses on a transparent, data-driven pipeline that combines AIS-based ship profiles with MRV-reported emission data to support risk-based inspection and compliance prioritization.

---

## 1. Use Case and Objective

The objective of this prototype is to support **FuelEU plausibility checks** by identifying vessels whose reported emission intensity deviates unusually from what would be expected based on their operational profile.

Rather than reconstructing physical fuel consumption, the system follows a **proxy-based, statistical approach**:
- AIS data is used to derive aggregated ship-level operating profiles,
- MRV data is validated and used as a reference signal,
- a regression model estimates an expected emission intensity,
- residual-based flagging highlights ships that warrant closer inspection.

The system is designed as an **analytical decision-support tool**.  
It does **not** replace official MRV reporting and does **not** issue compliance decisions.

---

## 2. Data Sources (Conceptual)

No raw data is stored in this repository.

Conceptually, the pipeline relies on:

- **AIS-derived vessel movement data**  
  High-frequency position data and static vessel characteristics (e.g. length, breadth, draft).

- **MRV-reported emission data**  
  Used as a reference signal after plausibility validation, not as a time-resolved ground truth.

- **Derived operational indicators**  
  Aggregated ship-level features describing typical operational behavior.

Due to size and licensing constraints, all raw and intermediate datasets are treated as external inputs and are excluded from version control.

---

## 3. End-to-End Pipeline Overview

The system is implemented as an ordered, notebook-driven pipeline located in `notebooks/pipeline/`.

The pipeline consists of **four clearly separated conceptual stages**:

### 1. AIS Data Aggregation

AIS data is cleaned and aggregated from high-frequency position records to a **ship-year level**.

The resulting ship profiles describe typical operating behavior rather than individual voyages.  
This aggregation aligns AIS data with the annual structure of MRV reporting.

Key feature groups include:
- **Static vessel characteristics**  
  (e.g. length, breadth, ship type, median draft as a loading proxy)
- **Speed-related indicators**  
  (median speed and 95th percentile as a proxy for maximum operating speed)
- **Activity indicators**  
  (e.g. moving share representing the fraction of time a vessel is underway)

These features do not fully describe a ship’s technical configuration, but capture core determinants of emission intensity.

---

### 2. MRV Data Validation

Before MRV data is used as a reference signal, its plausibility is assessed in two steps:

- **Internal consistency check**  
  A strong linear relationship between fuel consumption per nautical mile and CO₂ emissions per nautical mile confirms physical plausibility.

- **External plausibility check**  
  A regression-based comparison between MRV-reported emission intensity and AIS-derived ship profiles shows that a large share of variation can be explained by vessel characteristics and operational behavior.

Based on these checks, MRV emission intensity is accepted as a **plausible reference**, with the explicit understanding that plausibility does not imply perfect accuracy.

---

### 3. Regression for Expected Emission Intensity

Using the aggregated AIS ship profiles, a regression model is trained to estimate the **expected emission intensity** (kg CO₂ per nautical mile).

This target variable is:
- normalized,
- independent of total sailing time,
- directly comparable across ships.

The regression serves as a **baseline estimator**, not as a physical emission model.  
Despite missing direct fuel consumption data, the model achieves strong explanatory power, indicating that AIS-derived profiles capture key drivers of emission intensity.

This estimated expectation forms the basis for subsequent flagging.

---

### 4. Residual-Based Flagging for Inspection Prioritization

Flagging is based on the **residual** between:
- MRV-reported emission intensity, and
- regression-based expected emission intensity.

Residuals are evaluated along two dimensions:
- **Absolute deviation** (kg CO₂ per nautical mile), particularly relevant for larger vessels,
- **Relative deviation** (%), particularly relevant for smaller vessels.

A vessel is flagged when predefined tolerance thresholds are exceeded in at least one dimension.

This dual-threshold approach avoids systematic bias across vessel sizes and operating profiles.

A **Red Flag** indicates an inspection candidate, not a non-compliance verdict.  
A **Green Flag** indicates behavior within the plausibility range.

---

## 4. Repository Structure

The repository documents the logical system architecture.  
Data directories are conceptual and not versioned.

```text
fuel-eu-monitoring-prototype/
├── notebooks/
│   ├── pipeline/      # Ordered, pipeline-critical notebooks
│   └── archive/       # Exploratory and deprecated notebooks
│
├── data/              # Logical data structure (not versioned)
│   ├── raw/           # External raw input data (AIS, MRV)
│   ├── interim/       # Intermediate processing results
│   └── processed/     # Final consolidated outputs
│
├── src/               # Architectural skeleton for future modularization
│
├── reports/           # Generated tables and figures (not versioned)
│
├── .gitignore
└── README.md```

---

## 5. Reproducibility

The prototype is designed with a focus on **conceptual reproducibility** rather than full data replication.

Key aspects include:
- a deterministic, ordered execution of pipeline steps,
- a clear separation between data preparation, modeling, and flagging logic,
- transparent modeling assumptions and threshold definitions.

The system is implemented in **Python (tested with Python 3.11)** and executed via **Jupyter notebooks**.

Raw input data is excluded from version control due to size and licensing constraints.  
Reproducibility assumes that the required AIS- and MRV-related datasets are available locally and placed according to the documented directory structure.

---

## 6. Limitations

This prototype has several important limitations:

- Emission and fuel-related values are **proxy-based statistical estimates**, not physical reconstructions.
- Results depend on feature engineering choices, aggregation level, and regression assumptions.
- AIS data noise and reporting inconsistencies may affect residual calculations.
- The flagging logic supports **plausibility assessment and inspection prioritization**, not regulatory enforcement or compliance decisions.

Accordingly, the system should be interpreted as an analytical support tool rather than an operational compliance solution.

---

## 7. Future Work

Potential extensions of the prototype include:

- extraction of notebook-based logic into modular Python components,
- introduction of an explicit validation layer between data aggregation and modeling,
- automation of pipeline execution and configuration management,
- further documentation and sensitivity analysis of modeling assumptions and flagging thresholds.