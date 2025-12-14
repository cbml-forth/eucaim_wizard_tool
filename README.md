# EUCAIM Wizard Tool — ARX “project pack” for risk analysis

This repository is for **data curators / analysts** who want to run **re-identification risk analysis** and **utility evaluation** in the **ARX Data Anonymization Tool**, using the **EUCAIM Wizard configuration** (recommended workflow + pre-built hierarchies for DICOM metadata / clinical tabular data).

You need first:
1) export **tabular data** (`.csv` / `.xlsx`) containing **clinical fields and/or extracted DICOM metadata** (i.e. through the EUCAIM DICOM Tags Extractor),  
2) load it into **ARX**,  
3) import/select the provided **generalization hierarchies**,  
4) run **risk + utility** analysis, and  
5) export a **PDF report** and an **anonymized dataset**. 

> ⚠️ **Do not use real or non de-identified patient data** (no raw DICOM headers). This configuration assumes that DICOM images have already been de-identified. 

---

## 1) Prerequisites (what you need installed)

Install **ARX Data Anonymization Tool** (GUI). Official pages:
```
https://arx.deidentifier.org/anonymization-tool/
https://arx.deidentifier.org/downloads/
``` 

That’s it — you **do not need to build code** from this repo just to use the hierarchies/templates.

---

## 2) What’s in this repository

Typical folders:

- `hierarchies/`  
  Pre-built hierarchy files (CSV) for EUCAIM-relevant quasi-identifiers (QIs) — e.g., dates, nationality, manufacturer, alergies etc. 

- `examples/`  
  Small **synthetic** example datasets (CSV/XLSX) and example outputs (report/anonymized file).

- `docs/`  
  User guidance: how to import data, assign attribute roles, load hierarchies, run risk/utility analysis, and interpret results.

---

## 3) Before ARX: prepare your input table

### A. DICOM metadata
ARX works on **tabular data**, not on `.dcm` files directly.  
First, extract the relevant **DICOM tags** into a `.csv`/`.xlsx` (one study row per record), then analyze that table in ARX.

### B. Clinical / cohort data
Any cohort-style table works:
- **rows** = subjects/studies/records
- **columns** = demographics, lab values, conditions, therapies, imaging metadata fields, etc. 

### C. One cohort = one ARX project
Treat **imaging metadata** and **clinical information** as separate projects. 

---

## 4) Run the Wizard workflow in ARX (step-by-step)

### Step 1 — Create a new ARX project and import your CSV/XLSX
1. Open **ARX**
2. Create a **new project**
3. **Import** your dataset (`.csv` or `.xlsx`) 

### Step 2 — Assign attribute roles (this matters most)
For each column, decide whether it is:
- **Identifier**: direct identifiers (typically removed / not used for release)
- **Quasi-Identifier (QI)**: enables re-identification when combined (needs generalization/suppression)
- **Sensitive attribute**: values you want to protect (e.g., diagnoses)
- **Insensitive attribute**: safe to keep as-is

This classification is central to defining the **solution space** ARX will explore.

### Step 3 — Load/select hierarchies for QIs
For each QI column:
1. Import the matching **hierarchy CSV** from this repository, inside the /hierarchies folder
2. Confirm hierarchy levels are correct (from specific → more general) 

Example hierarchy idea (date masking like `YYYYMMDD → YYYYMM* → YYYY****`)

### Step 4 — Choose a privacy / risk model
Select an anonymization model appropriate to your context, commonly:
- **k-anonymity** (and extensions)
- optionally define limits for **generalization/suppression**
- optionally allow a small percentage of **outliers** to be ignored (if appropriate)

### Step 5 — Run analysis and explore results
Run the analysis. Then use ARX’s **Explore Results** view to:
- compare candidate transformations
- filter by compliance (color-coded)
- understand which attributes drive risk and which drive utility loss

### Step 6 — Utility analysis (don’t skip this)
After selecting a candidate solution, evaluate **usefulness**:
- generic **information-loss metrics**
- (optional) **classification models** to compare performance on original vs anonymized data

### Step 7 — Export
Export:
- a **PDF report** describing the selected generalization levels and results, and
- the **anonymized dataset** (`.csv`/`.xlsx`)

---

## 5) Using (and editing) hierarchy files

Hierarchy files are typically CSV tables with multiple generalization levels per value.

Example concept (nationality, 3 levels):
- Level 0: specific value
- Level 1: broader grouping
- Level 2: coarsest grouping

Tips:
- Keep hierarchies **consistent** with your column’s value format
- Version-control hierarchy changes so analyses remain reproducible

---

## 6) Common problems & fixes

- **“Invalid input dataset format”**  
  Check CSV delimiter/encoding, header row, and consistent column types.

- **“Too strict anonymity requirements” / no feasible solutions**  
  Try one or more of:
  - relax k / privacy parameters (if allowed)
  - allow more suppression
  - use coarser hierarchies
  - reduce the number of QIs (only if justified) 

- **Utility loss is too high**  
  Consider alternative hierarchies, attribute weights, or different constraints; compare utility metrics and/or classification performance.

---
