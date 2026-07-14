Here is a clean **README.md section format** for your ViralWatch project. You can directly add this to your GitHub README.

```markdown
# Incremental Data Update Pipeline

## Overview

ViralWatch uses an incremental data update pipeline to continuously integrate the latest outbreak information from the INRB-UMIE/BDBV2026-Data repository.

Instead of reprocessing the entire dataset every time new outbreak reports are released, the pipeline identifies only new or modified outbreak records, applies the existing cleaning rules, updates the processed dataset, refreshes the database, and generates updated risk predictions for the dashboard.

This approach enables ViralWatch to function as a near real-time early-warning system.

---

## Data Update Workflow

```

INRB-UMIE Repository
|
| (automatic update check)
↓
New Raw Outbreak Data
|
| (detect new/changed records)
↓
Incremental Cleaning Pipeline
|
| (append + update)
↓
Existing Clean Dataset
|
↓
SQLite Database
|
↓
Machine Learning Models
|
↓
FastAPI Service
|
↓
Cross-Border Dashboard

```

---

# Data Organization

The project maintains three data layers:

```

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
└── Historical cleaned outbreak dataset

```

### Data Layers Explanation

| Layer | Purpose |
|---|---|
| `data/external/` | Original outbreak data from INRB-UMIE. This data is never modified. |
| `data/raw/incoming/` | Temporary storage for newly downloaded updates. |
| `data/processed/` | Clean, analysis-ready dataset used by ML models. |

---

# Incremental Update Process

## 1. Track Previously Processed Data

The cleaned dataset stores historical outbreak records:

`data/processed/outbreak_clean.csv`

Example:

| date | health_zone | confirmed | deaths |
|---|---|---|---|
|2026-07-13|Goma|100|20|
|2026-07-13|Beni|50|10|

The pipeline identifies the latest processed date:

```

Last processed date: 2026-07-13

````

---

# 2. Fetch Latest INRB Data

The pipeline checks the INRB repository for updates:

```bash
cd data/external/BDBV2026-Data

git pull
````

If new outbreak reports are available:

```
Existing data:
2026-07-13 report

New data:
2026-07-14 report
```

The pipeline detects:

```
New records found:
2026-07-14
```

---

# 3. Detect New or Changed Records

The pipeline compares the new raw data with the existing cleaned dataset.

Example:

```python
import pandas as pd


old_data = pd.read_csv(
    "data/processed/outbreak_clean.csv"
)


new_data = pd.read_csv(
    "data/external/BDBV2026-Data/latest.csv"
)


last_date = old_data["date"].max()


new_records = new_data[
    new_data["date"] > last_date
]


print(new_records)
```

Output:

```
New records:

2026-07-14  Goma      120 cases
2026-07-14  Beni       60 cases
```

---

# 4. Apply Data Cleaning Rules

The same cleaning process used during initial data preparation is applied to the new records.

Cleaning operations include:

* Standardizing health-zone names.
* Converting dates.
* Removing duplicates.
* Handling missing values.
* Correcting inconsistent formats.

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

# 5. Merge Updated Records

New cleaned records are merged with the existing processed dataset.

```python
updated_data = pd.concat(
    [
        old_data,
        new_records
    ]
)


updated_data = updated_data.drop_duplicates()
```

Before:

```
2026-07-13  Goma  100
```

After update:

```
2026-07-13  Goma  100
2026-07-14  Goma  120
```

The updated dataset is saved:

```python
updated_data.to_csv(
    "data/processed/outbreak_clean.csv",
    index=False
)
```

---

# 6. Update Database

The processed dataset updates the ViralWatch database:

```
Processed Dataset
        |
        ↓
SQLite Database
        |
        ↓
FastAPI API
        |
        ↓
Dashboard
```

The database contains:

* Health-zone information.
* Daily case counts.
* Engineered features.
* Model predictions.
* Risk scores.

---

# 7. Refresh Machine Learning Predictions

After new data is added:

1. Feature engineering runs again.
2. Models generate new predictions.
3. Risk scores are updated.

Example:

```
Health Zone        Risk Level

Goma               High
Rutshuru           High
Beni               Medium
Bukavu             Low
```

---

# 8. Automated Daily Updates

The pipeline can run automatically using Linux Cron.

Edit scheduled tasks:

```bash
crontab -e
```

Add:

```bash
0 8 * * * /home/sitanu/viralwatch/scripts/update_pipeline.sh
```

This runs every day at 08:00.

The automated workflow:

```
1. Pull latest INRB data
2. Detect new outbreak records
3. Clean new records
4. Update processed dataset
5. Update SQLite database
6. Generate new predictions
7. Refresh dashboard
```

---

# Live Demo Workflow

During the final demonstration:

Run:

```bash
./scripts/update_pipeline.sh
```

Expected output:

```
✓ Checking INRB updates
✓ New outbreak records detected
✓ Cleaning completed
✓ Processed dataset updated
✓ Database refreshed
✓ Risk predictions generated
✓ Dashboard updated
```

The dashboard then displays the latest cross-border outbreak risk information for:

* North Kivu health zones
* South Kivu health zones
* Areas bordering Rwanda

---

## Benefits of Incremental Updating

* Avoids unnecessary full dataset processing.
* Supports daily outbreak surveillance.
* Handles continuously changing epidemiological data.
* Keeps ML predictions current.
* Enables real-time dashboard updates.

```

This section fits well under **"Data Pipeline Architecture"** in your ViralWatch README.
```
