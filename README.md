# Data Sources

ViralWatch integrates multiple datasets from the **INRB-UMIE/BDBV2026-Data** repository to support outbreak surveillance, machine learning, and cross-border risk monitoring.

Each dataset provides specific information that contributes to different components of the ViralWatch pipeline.

---

## Primary Data Sources

### 1. `insp_sitrep/`
**Purpose:** Daily epidemiological situation reports.

**Data Includes:**
- Daily confirmed cases
- Daily suspected cases
- Daily deaths
- Hospitalizations
- Contact tracing
- Isolation statistics

**Used For:**
- Epidemic curve visualization
- Case-fatality ratio analysis
- Machine learning feature generation
- Dashboard updates

**Priority:** ⭐⭐⭐⭐⭐

---

### 2. `epi/`
**Purpose:** Weekly WHO epidemiological reports.

**Data Includes:**
- Weekly outbreak summaries
- National surveillance statistics
- Disease progression

**Used For:**
- Weekly trend analysis
- Historical outbreak comparison

**Priority:** ⭐⭐⭐⭐

---

### 3. `worldpop/`
**Purpose:** Population statistics.

**Data Includes:**
- Population count
- Population density

**Used For:**
- Machine learning features
- Risk normalization
- Population-based analysis

**Priority:** ⭐⭐⭐⭐⭐

---

### 4. `osrm/`
**Purpose:** Road network accessibility.

**Data Includes:**
- Road travel time
- Road distance between health zones

**Used For:**
- Accessibility analysis
- Machine learning features
- Healthcare response planning

**Priority:** ⭐⭐⭐⭐⭐

---

### 5. `shapefiles/`
**Purpose:** Geographic boundaries of DRC health zones.

**Data Includes:**
- Health zone polygons
- Administrative boundaries

**Used For:**
- Interactive maps
- Geospatial visualization
- Cross-border dashboard

**Priority:** ⭐⭐⭐⭐⭐

---

### 6. `cross-border-movements/`
**Purpose:** Cross-border mobility data.

**Data Includes:**
- Border crossing estimates
- Passenger movement statistics

**Used For:**
- Cross-border outbreak monitoring
- Risk assessment

**Priority:** ⭐⭐⭐⭐

---

### 7. `flowminder/`
**Purpose:** Human mobility information.

**Data Includes:**
- Population inflow
- Population outflow
- Movement estimates

**Used For:**
- Disease spread modeling
- Mobility analysis

**Priority:** ⭐⭐⭐⭐

---

### 8. `testing_capacity/`
**Purpose:** Laboratory testing resources.

**Data Includes:**
- PCR machine availability
- Testing capacity

**Used For:**
- Healthcare preparedness analysis
- Laboratory capacity assessment

**Priority:** ⭐⭐⭐

---

### 9. `grid3_healthsites/`
**Purpose:** Healthcare facility locations.

**Data Includes:**
- Health facility count
- Health facility density

**Used For:**
- Healthcare accessibility
- Resource availability analysis

**Priority:** ⭐⭐⭐⭐

---

### 10. `healthsites_io/`
**Purpose:** Open-source healthcare facility dataset.

**Data Includes:**
- Healthcare facility locations
- Facility counts

**Used For:**
- Additional healthcare infrastructure mapping

**Priority:** ⭐⭐⭐

---

### 11. `public_health_response/`
**Purpose:** Public health intervention indicators.

**Data Includes:**
- Coordination activities
- Laboratory response
- Surveillance
- Community engagement
- Infection prevention and control

**Used For:**
- Public health response monitoring
- Dashboard context information

**Priority:** ⭐⭐⭐

---

### 12. `refugee_sites/`
**Purpose:** Refugee settlement information.

**Data Includes:**
- Refugee site locations
- Refugee site counts

**Used For:**
- Population vulnerability assessment
- Humanitarian response planning

**Priority:** ⭐⭐

---

# Summary

| Dataset | Description | Primary Use | Priority |
|----------|-------------|-------------|----------|
| `insp_sitrep` | Daily outbreak situation reports | Epidemiological analysis & ML | ⭐⭐⭐⭐⭐ |
| `epi` | Weekly WHO epidemiological reports | Trend analysis | ⭐⭐⭐⭐ |
| `worldpop` | Population statistics | Population features | ⭐⭐⭐⭐⭐ |
| `osrm` | Road travel time & distance | Accessibility analysis | ⭐⭐⭐⭐⭐ |
| `shapefiles` | Health zone boundaries | Mapping & visualization | ⭐⭐⭐⭐⭐ |
| `cross-border-movements` | Border movement estimates | Cross-border surveillance | ⭐⭐⭐⭐ |
| `flowminder` | Human mobility | Disease spread analysis | ⭐⭐⭐⭐ |
| `testing_capacity` | PCR testing resources | Healthcare preparedness | ⭐⭐⭐ |
| `grid3_healthsites` | Health facility locations | Healthcare accessibility | ⭐⭐⭐⭐ |
| `healthsites_io` | Additional health facilities | Infrastructure mapping | ⭐⭐⭐ |
| `public_health_response` | Public health response indicators | Response monitoring | ⭐⭐⭐ |
| `refugee_sites` | Refugee settlement data | Vulnerability assessment | ⭐⭐ |


# Incremental Data Update Pipeline

## Overview

ViralWatch implements an **incremental data update pipeline** to continuously integrate new outbreak information from the **INRB-UMIE/BDBV2026-Data repository**.

Instead of reprocessing the complete outbreak dataset whenever new reports become available, the pipeline identifies only **new or modified records**, applies the existing data-cleaning rules, updates the processed dataset, refreshes the database, and generates new outbreak risk predictions.

This design enables ViralWatch to operate as a **near real-time viral haemorrhagic fever early-warning system**.

---

# Data Pipeline Architecture

```text
INRB-UMIE BDBV2026 Repository
              |
              | Automatic update check
              ↓
      New Raw Outbreak Data
              |
              | Detect new/changed records
              ↓
     Incremental Cleaning Pipeline
              |
              | Append + Update
              ↓
      Processed Clean Dataset
              |
              ↓
        SQLite Database
              |
              ↓
      Machine Learning Models
              |
              ↓
        FastAPI Backend
              |
              ↓
   Cross-Border Risk Dashboard
```

---

# Data Organization

ViralWatch maintains a structured data lifecycle with three main data layers:

```text
data/
│
├── external/
│   └── BDBV2026-Data/
│       └── Original INRB-UMIE outbreak dataset
│
├── raw/
│   └── incoming/
│       └── Newly downloaded outbreak updates
│
└── processed/
    └── outbreak_clean.csv
        └── Analysis-ready outbreak dataset
```

## Data Layer Description

| Data Layer           | Purpose                                                                                            |
| -------------------- | -------------------------------------------------------------------------------------------------- |
| `data/external/`     | Stores the original outbreak dataset obtained from INRB-UMIE. This source data remains unchanged.  |
| `data/raw/incoming/` | Temporary storage location for newly collected outbreak updates before processing.                 |
| `data/processed/`    | Contains cleaned and validated data used by analytics, machine learning models, and the dashboard. |

---

# Incremental Update Workflow

## 1. Identify Existing Processed Data

The pipeline first loads the previously processed outbreak dataset:

```
data/processed/outbreak_clean.csv
```

Example:

| Date       | Health Zone | Confirmed Cases | Deaths |
| ---------- | ----------- | --------------- | ------ |
| 2026-07-13 | Goma        | 100             | 20     |
| 2026-07-13 | Beni        | 50              | 10     |

The latest processed date is extracted:

```text
Last processed date:
2026-07-13
```

This date is used as the reference point for detecting new outbreak information.

---

# 2. Retrieve Latest Outbreak Data

The pipeline checks the INRB repository for new updates.

```bash
cd data/external/BDBV2026-Data

git pull
```

Example:

Previous dataset:

```
2026-07-13 outbreak report
```

New dataset:

```
2026-07-14 outbreak report
```

Detected update:

```
New records available:
2026-07-14
```

---

# 3. Detect New or Modified Records

The pipeline compares incoming data with the existing processed dataset.

Example implementation:

```python
import pandas as pd

existing_data = pd.read_csv(
    "data/processed/outbreak_clean.csv"
)

latest_data = pd.read_csv(
    "data/external/BDBV2026-Data/latest.csv"
)

last_date = existing_data["date"].max()

new_records = latest_data[
    latest_data["date"] > last_date
]

print(new_records)
```

Output:

```text
New outbreak records:

2026-07-14 | Goma  | 120 cases
2026-07-14 | Beni  | 60 cases
```

---

# 4. Apply Data Cleaning Rules

New outbreak records go through the same preprocessing pipeline used during initial dataset preparation.

Cleaning operations include:

* Standardizing health-zone names.
* Converting date formats.
* Removing duplicate records.
* Handling missing values.
* Correcting inconsistent data formats.

Example:

```python
new_records["health_zone"] = (
    new_records["health_zone"]
    .str.strip()
    .str.lower()
)

new_records["date"] = pd.to_datetime(
    new_records["date"]
)
```

---

# 5. Update Processed Dataset

Cleaned records are merged with historical outbreak data.

```python
updated_dataset = pd.concat(
    [
        existing_data,
        new_records
    ]
)

updated_dataset = updated_dataset.drop_duplicates()
```

Updated dataset:

Before:

```text
2026-07-13 | Goma | 100 cases
```

After:

```text
2026-07-13 | Goma | 100 cases
2026-07-14 | Goma | 120 cases
```

The final dataset is saved:

```python
updated_dataset.to_csv(
    "data/processed/outbreak_clean.csv",
    index=False
)
```

---

# 6. Database Synchronization

After processing, the updated dataset refreshes the ViralWatch database.

```text
Processed Dataset
        |
        ↓
 SQLite Database
        |
        ↓
 FastAPI Backend
        |
        ↓
 Dashboard
```

The database stores:

* Health-zone information.
* Daily outbreak statistics.
* Engineered epidemiological features.
* Model predictions.
* Risk scores.

---

# 7. Machine Learning Prediction Update

Whenever new outbreak data is available:

1. Feature engineering is executed.
2. Machine learning models process updated information.
3. Risk predictions are regenerated.
4. Dashboard indicators are refreshed.

Example output:

| Health Zone | Risk Level |
| ----------- | ---------- |
| Goma        | High       |
| Rutshuru    | High       |
| Beni        | Medium     |
| Bukavu      | Low        |

---

# Automated Daily Update

The update pipeline can run automatically using Linux Cron.

Edit scheduled jobs:

```bash
crontab -e
```

Add:

```bash
0 8 * * * /home/sitanu/viralwatch/scripts/update_pipeline.sh
```

This executes the update process every day at **08:00 AM**.

Automated workflow:

```text
1. Pull latest INRB outbreak data
2. Detect new records
3. Clean incoming data
4. Update processed dataset
5. Synchronize database
6. Generate risk predictions
7. Refresh dashboard
```

---

# Live Demonstration

For the final demonstration, run:

```bash
./scripts/update_pipeline.sh
```

Expected output:

```text
✓ Checking INRB repository updates
✓ New outbreak records detected
✓ Data cleaning completed
✓ Processed dataset updated
✓ Database synchronized
✓ Risk predictions generated
✓ Dashboard refreshed
```

The dashboard displays updated cross-border outbreak risk information covering:

* North Kivu health zones
* South Kivu health zones
* Border regions connected to Rwanda

---

# Benefits of Incremental Updating

The incremental pipeline provides:

✅ Faster data processing compared with full dataset rebuilding
✅ Continuous outbreak surveillance capability
✅ Reliable handling of changing epidemiological information
✅ Updated machine learning predictions
✅ Near real-time dashboard monitoring
✅ Scalable architecture for future outbreak sources

---

## Summary

The ViralWatch incremental update pipeline creates a reliable bridge between outbreak data collection, machine learning prediction, and public-health decision support.

By continuously integrating new information while preserving historical records, ViralWatch provides an efficient foundation for early detection and cross-border viral haemorrhagic fever monitoring.
