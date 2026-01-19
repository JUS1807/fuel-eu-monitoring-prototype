# FuelEU Monitoring Prototype

This repository contains a prototype implementation for monitoring and assessing ship-level fuel consumption and emissions under the EU FuelEU Maritime framework.
The project was developed as part of a logistics case challenge and focuses on building a transparent, data-driven pipeline from raw AIS-derived inputs to compliance-relevant outputs.

---

## 1. Use Case and Objective

The objective of this prototype is to estimate fuel consumption and CO₂-related metrics at ship level based on AIS-derived movement data and additional vessel characteristics, and to derive compliance-relevant indicators.

The system is designed as an analytical decision-support tool. It does not replace official MRV reporting, but provides a scalable proxy-based approach to:
- monitor vessel behavior,
- identify anomalies or inefficiencies,
- support compliance checks under FuelEU Maritime.

---

## 2. Data Sources (Conceptual)

No raw data is stored in this repository.

Conceptually, the pipeline builds on:
- AIS-derived vessel movement data
- Vessel-level technical characteristics
- Derived operational indicators (e.g. speed, distance, activity patterns)

All raw and intermediate datasets are treated as external inputs and are excluded from version control.

---

## 3. Pipeline Overview

The analytical pipeline is implemented primarily via ordered Jupyter notebooks located in `notebooks/pipeline/`.

The processing logic follows a strict sequence:

1. Preprocessing  
   Cleaning and standardization of AIS- and MRV-related input data.

2. Initial Feature Derivation  
   Computation of first-order operational indicators and proxy variables.

3. Feature Enrichment  
   Refinement and enrichment of derived features to improve robustness and consistency.

4. Aggregation  
   Aggregation of movement and feature data to a ship-year level.

5. Model Dataset Construction  
   Assembly of a consistent, model-ready dataset.

6. Regression & Flagging Logic  
   Estimation of fuel/emission proxies and derivation of residual-based compliance indicators.

7. Final Output & Interpretation  
   Validation, explanation, and preparation of result tables for reporting.

---

## 4. Repository Structure

The repository follows a clear separation between code, notebooks, and data.
Raw and processed data are not versioned and are expected to be available locally.


```
fuel-eu-monitoring-prototype/
├── notebooks/
│   ├── pipeline/      # Ordered, pipeline-critical notebooks
│   └── archive/       # Exploratory and deprecated notebooks
│
├── data/              # Logical data structure (not versioned)
│   ├── raw/           # External raw input data (AIS, MRV, etc.)
│   ├── interim/       # Intermediate processing results
│   └── processed/     # Final model-ready datasets and outputs
│
├── src/               # Architectural skeleton for modularization
│
├── reports/           # Generated tables and figures (not versioned)
│
├── .gitignore
└── README.md
```

---

## 5. Reproducibility

The repository is structured to ensure conceptual reproducibility:
- deterministic, ordered pipeline steps,
- explicit separation between raw, interim, and processed data,
- clear distinction between exploratory and pipeline-critical code.

Execution currently assumes that required input data is available locally and paths are configured accordingly.
The prototype is implemented in Python (tested with Python 3.11) and executed via Jupyter notebooks.

### Data Availability

Due to size and licensing constraints, raw input data is not included in this repository.
The pipeline assumes that AIS- and MRV-related input data is available locally and
placed in the corresponding `data/raw/` directory.

---

## 6. Limitations

- Fuel consumption and emission values are proxy-based estimates.
- Results depend on assumptions embedded in feature construction and regression modeling.
- Data gaps, AIS noise, and aggregation choices may affect accuracy.

The prototype is intended for analytical exploration and methodological demonstration, not for regulatory reporting.

---

## 7. Future Work

- Extraction of notebook logic into modular Python scripts
- Explicit validation layer between preprocessing and modeling
- Automated pipeline execution and configuration management
- Extended documentation of modeling assumptions