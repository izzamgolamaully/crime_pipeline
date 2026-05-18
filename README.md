# Crime Data Pipeline & Reporting Dataset

**Rockborne Data Training Programme — Phase 1**
**Role:** Data Engineer, Police Force Analytics Unit

---

## Project Overview

This project builds an end-to-end Python data pipeline that ingests raw UK police crime data for four police forces, enriches it with contextual datasets, and produces a single BI-ready reporting CSV for Power BI consumption.

The pipeline covers **April 2023 – March 2026** across four forces:
- Metropolitan Police
- West Midlands Police
- West Yorkshire Police
- Thames Valley Police

---

## Repository Structure

```
crime-pipeline/
│
├── data/
│   ├── raw/
│   │   ├── police/          ← UK Police crime CSVs (not tracked — see below)
│   │   └── enrichment/      ← ONS population, IMD, employment files (not tracked)
│   └── outputs/             ← Final reporting CSV (not tracked)
│
├── notebooks/
│   └── crime_pipeline.ipynb ← Main pipeline notebook
│
├── docs/
│   ├── Statement_of_Work.docx
│   ├── Data_Dictionary.xlsx
│   └── Workflow_Diagram.png
│
├── .gitignore
└── README.md
```

> **Note:** Raw data files and output CSVs are excluded from this repository via `.gitignore`. See the Data Sources section below for download instructions.

---

## Pipeline Stages

The notebook is structured as a linear, repeatable pipeline:

| Stage | Description |
|---|---|
| 1. Ingestion | Batch-loads 144 CSVs by force using a for-loop; unions into ~5.9M rows |
| 2. Cleaning & Validation | Null checks, deduplication, category standardisation, date validation |
| 3. Feature Engineering | Derives year, month, severity weights (Cambridge Crime Harm Index) |
| 4. Enrichment & Aggregation | Joins population, IMD, and employment data; aggregates to reporting grain |
| 5. Export & Validation | Exports final CSV; runs summary validation checks |

**Reporting grain:** Police Force × Year-Month × Crime Category

---

## Data Sources

### Primary Dataset
- **UK Police Crime Data** — [data.police.uk/data/](https://data.police.uk/data/)
- Select: Metropolitan Police, West Midlands Police, West Yorkshire Police, Thames Valley Police
- Period: April 2023 – March 2026
- Extract all CSVs to `data/raw/police/`

### Enrichment Datasets

| Dataset | Source | Save as |
|---|---|---|
| ONS Police Force Area Population Estimates | [ons.gov.uk](https://www.ons.gov.uk/peoplepopulationandcommunity/crimeandjustice/datasets/policeforceareadatatables) — Year ending Dec 2025 | `data/raw/enrichment/pfatablesyedec2025.xlsx` |
| English Indices of Deprivation 2019 (IMD) | [gov.uk](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019) — File 1 | `data/raw/enrichment/File_1_-_IMD2019_Index_of_Multiple_Deprivation.xlsx` |
| ONS Regional Employment Rates | ONS Annual Population Survey — embedded in notebook as documented lookup table | N/A |

---

## Setup & Requirements

### Prerequisites
- Python 3.9+
- Jupyter Notebook or JupyterLab
- Anaconda recommended

### Install dependencies

```bash
pip install pandas numpy openpyxl
```

### Run the pipeline

1. Download all data sources listed above and place in the correct folders
2. Open `notebooks/crime_pipeline.ipynb`
3. Run all cells top to bottom (Kernel → Restart & Run All)
4. Final CSV will be written to `data/outputs/crime_reporting_dataset.csv`

---

## Output

The pipeline produces a single file: `crime_reporting_dataset.csv`

**Shape:** 2,016 rows × 15 columns

**Key fields:**

| Field | Description |
|---|---|
| `force_name` | Standardised police force name |
| `year_month` | First day of reporting month (YYYY-MM-01) |
| `crime_category` | Home Office standardised crime type (14 categories) |
| `crime_count` | Total crimes at this grain |
| `crime_rate_per_1000` | Crimes per 1,000 residents (ONS mid-2024 population) |
| `severity_weighted_count` | Crime count weighted by Cambridge Crime Harm Index |
| `imd_avg_decile` | Average IMD 2019 deprivation decile for the force area |
| `employment_rate` | Annual regional employment rate (16–64, %) |

Full column definitions are in `docs/Data_Dictionary.xlsx`.

---

## Key Assumptions

| # | Assumption |
|---|---|
| A1 | ONS mid-2024 population figures applied across all years (2023–2026) |
| A2 | IMD 2019 deprivation scores treated as static across all years |
| A3 | ONS employment rates applied annually; 2025 figures used for Jan–Mar 2026 |
| A4 | Crime categories standardised to 14 Home Office labels |
| A5 | Greater Manchester Police unavailable on data.police.uk at download; replaced by West Midlands Police |
| A6 | Data availability limited to April 2023 (rolling window); pipeline is date-agnostic |

---

## Documentation

- `docs/Statement_of_Work.docx` — Project scope, datasets, timeline, assumptions
- `docs/Data_Dictionary.xlsx` — Full column definitions, data types, sources, calculations
- `docs/Workflow_Diagram.png` — Visual pipeline flow: Ingestion → Transformation → Export

---

## GitHub

This project was uploaded to GitHub sequentially to reflect the full development process.
Each stage of the pipeline, including data ingestion, preprocessing, database integration, 
analysis, and automation, was committed separately to maintain a clear and traceable commit history.

The repository history documents the progression of the project from initial setup through to the 
final implementation, allowing changes and feature additions to be reviewed step by step.
🔗 https://github.com/izzamgolamaully/crime_pipeline
---

## Author

Izzam — Rockborne Data Training Programme, May 2026
