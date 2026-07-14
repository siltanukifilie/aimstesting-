Copy the complete content below into your `README.md` file.

```markdown
# ViralWatch
## AI-Powered Viral Haemorrhagic Fever Outbreak Early-Warning System

**KTT Fellows · Week 1 Capstone Project · July 2026**

---

# 1. Project Overview

ViralWatch is an end-to-end AI-powered early-warning system designed to detect abnormal outbreak patterns, predict health-zone risk, extract signals from public health reports, and provide a real-time cross-border surveillance dashboard.

The system focuses on the 2026 Bundibugyo virus outbreak in the Democratic Republic of Congo (DRC), with particular attention to North Kivu and South Kivu health zones bordering Rwanda.

The platform combines:

- Data engineering pipelines
- Epidemiological data processing
- Machine learning prediction
- Anomaly detection
- Natural Language Processing (NLP)
- FastAPI backend services
- Real-time dashboard visualization

The objective is to identify outbreak signals earlier and support faster public health response.

---

# 2. System Architecture

```

```
                     ┌─────────────────────────┐
                     │   Data Sources          │
                     │                         │
                     │ INRB-UMIE Dataset       │
                     │ WHO Reports             │
                     │ HDX Health Zones        │
                     │ ECDC Updates            │
                     └────────────┬────────────┘
                                  │
                                  ▼

                     ┌─────────────────────────┐
                     │ Data Acquisition Layer  │
                     │                         │
                     │ Bash Scripts            │
                     │ Git Pull Updates        │
                     │ curl Downloads          │
                     └────────────┬────────────┘
                                  │
                                  ▼

                     ┌─────────────────────────┐
                     │ Data Processing Layer   │
                     │                         │
                     │ pandas                  │
                     │ NumPy                   │
                     │ Data Cleaning           │
                     │ Feature Engineering     │
                     └────────────┬────────────┘
                                  │
                                  ▼

                     ┌─────────────────────────┐
                     │ Database Layer          │
                     │                         │
                     │ SQLite                  │
                     │ Cases                   │
                     │ Health Zones            │
                     │ Features                │
                     │ Predictions             │
                     └────────────┬────────────┘
                                  │
                                  ▼

          ┌───────────────────────┴───────────────────────┐
          ▼                                               ▼
```

┌─────────────────────────┐                    ┌─────────────────────────┐
│ Machine Learning Layer  │                    │ NLP Processing Layer    │
│                         │                    │                         │
│ Case Prediction Model   │                    │ WHO Report Analysis     │
│ Anomaly Detection       │                    │ Entity Extraction       │
└────────────┬────────────┘                    └────────────┬────────────┘

```
          └───────────────────────┬───────────────────────┘
                                  │
                                  ▼

                     ┌─────────────────────────┐
                     │ FastAPI Backend         │
                     │                         │
                     │ /predict/{zone}         │
                     │ /earlywarning           │
                     │ /briefing              │
                     └────────────┬────────────┘
                                  │
                                  ▼

                     ┌─────────────────────────┐
                     │ Cross-Border Dashboard  │
                     │                         │
                     │ North Kivu              │
                     │ South Kivu              │
                     │ Rwanda Border Watch     │
                     └─────────────────────────┘
```

```

---

# 3. Project Structure

```

viralwatch/

│
├── data/
│   │
│   ├── external/
│   │   └── BDBV2026-Data/
│   │       └── Original INRB outbreak dataset
│   │
│   ├── raw/
│   │   └── incoming/
│   │       └── New outbreak updates
│   │
│   ├── processed/
│   │   └── outbreak_clean.csv
│   │
│   └── database/
│       └── viralwatch.db
│
├── notebooks/
│
├── src/
│   ├── data/
│   │   ├── load_data.py
│   │   └── clean_data.py
│   │
│   ├── features/
│   │   └── feature_engineering.py
│   │
│   └── models/
│       ├── train_model.py
│       └── predict.py
│
├── api/
│   └── main.py
│
├── frontend/
│
├── models/
│
├── scripts/
│   ├── download_data.sh
│   ├── update_pipeline.sh
│   └── run_pipeline.sh
│
├── requirements.txt
│
└── README.md

```

---

# 4. Incremental Data Update Pipeline

## Overview

ViralWatch uses an incremental data update pipeline to continuously integrate the latest outbreak information.

Instead of rebuilding the entire dataset every time new reports are released, the system:

1. Checks for new outbreak updates.
2. Detects new or modified records.
3. Applies existing cleaning rules.
4. Updates the processed dataset.
5. Refreshes the database.
6. Generates new risk predictions.
7. Updates the dashboard.

---

# 5. Incremental Data Workflow

```

INRB-UMIE Repository
|
|
| Daily Update Check
| git pull
↓

New Raw Outbreak Data

```
    |
    |
    | Detect New / Changed Records
    ↓
```

Cleaning Pipeline

```
    |
    |
    | Append + Update
    ↓
```

Processed Clean Dataset

```
    |
    ↓
```

SQLite Database

```
    |
    ↓
```

Machine Learning Models

```
    |
    ↓
```

FastAPI Service

```
    |
    ↓
```

Live Dashboard

```

---

# 6. Data Management Strategy

ViralWatch separates data into different layers.

```

data/

├── external/
│   └── BDBV2026-Data/
│       └── Original INRB data

├── raw/
│   └── incoming/
│       └── New downloaded updates

└── processed/
└── outbreak_clean.csv
└── Machine-learning-ready data

````

## Data Responsibilities

| Folder | Purpose |
|---|---|
| external | Original data source. Never modified. |
| raw | Temporary incoming outbreak updates. |
| processed | Cleaned data used by ML models. |
| database | Structured data used by APIs. |

---

# 7. Update Process

## Step 1: Pull Latest Outbreak Data

```bash
cd data/external/BDBV2026-Data

git pull origin main
````

Example:

Before:

```
2026-07-13 report
```

After:

```
2026-07-13 report
2026-07-14 report
```

---

# Step 2: Detect New Records

The system compares:

```
Existing Clean Dataset

        +

Latest INRB Dataset

        ↓

New / Updated Records
```

Example:

```
New record detected:

Date: 2026-07-14
Health Zone: Goma
Cases: 120
```

---

# Step 3: Clean New Data

Cleaning operations:

* Health-zone name standardization
* Missing value handling
* Date conversion
* Duplicate removal
* Data validation

Example:

```python
df["health_zone"] = (
    df["health_zone"]
    .str.strip()
    .str.lower()
)

df["date"] = pd.to_datetime(df["date"])
```

---

# Step 4: Update Processed Dataset

Old data:

```
2026-07-13 | Goma | 100 cases
```

New data:

```
2026-07-13 | Goma | 100 cases
2026-07-14 | Goma | 120 cases
```

Saved as:

```
data/processed/outbreak_clean.csv
```

---

# Step 5: Update Database

Processed data updates:

```
Processed Dataset

        ↓

SQLite Database

        ↓

FastAPI

        ↓

Dashboard
```

Database tables:

```
viralwatch.db

├── health_zones
├── daily_cases
├── features
├── predictions
└── alerts
```

---

# 8. Machine Learning Pipeline

The updated data is used for:

## Supervised Prediction Model

Predict:

> Probability that a health zone will report new cases in the next 7 days.

Models:

* Scikit-learn baseline
* Keras neural network

## Anomaly Detection

Detect unusual outbreak patterns before official confirmation.

Model:

* One-Class SVM

---

# 9. FastAPI Service

The backend provides:

## Predict Risk

```
GET /predict/{zone}
```

Example response:

```json
{
 "zone": "Goma",
 "risk": 0.92,
 "level": "High"
}
```

---

## Early Warning

```
GET /earlywarning
```

Returns ranked health zones by risk score.

---

## WHO Briefing

```
GET /briefing
```

Returns NLP-generated outbreak summaries.

---

# 10. Dashboard

The dashboard displays:

* Current outbreak risk.
* Cross-border health-zone watchlist.
* North Kivu alerts.
* South Kivu alerts.
* Latest WHO briefing.

Data flow:

```
Dashboard

     ↓

FastAPI

     ↓

SQLite Database

     ↓

Processed Outbreak Data
```

---

# 11. Automated Daily Execution

The pipeline can run automatically using Linux Cron.

Edit:

```bash
crontab -e
```

Add:

```bash
0 8 * * * /home/sitanu/viralwatch/scripts/update_pipeline.sh
```

The system runs every day at 08:00.

---

# 12. Complete Update Command

Run:

```bash
./scripts/update_pipeline.sh
```

The script performs:

```
✓ Pull latest INRB data

✓ Detect new outbreak records

✓ Clean new data

✓ Update processed dataset

✓ Update SQLite database

✓ Generate predictions

✓ Refresh dashboard
```

---

# 13. Final Live Demo Workflow

During the final presentation:

## 1. Update Data

```bash
./scripts/update_pipeline.sh
```

Output:

```
✓ New outbreak data detected
✓ Cleaning completed
✓ Database updated
✓ Risk scores generated
```

---

## 2. Start API

```bash
uvicorn api.main:app --reload
```

Open:

```
http://localhost:8000/docs
```

---

## 3. Launch Dashboard

The dashboard displays:

```
ViralWatch Cross-Border Alert

North Kivu

Goma          HIGH RISK
Rutshuru      HIGH RISK
Beni          MEDIUM


South Kivu

Bukavu        LOW
Uvira         MEDIUM
```

---

# 14. Key Benefits

This architecture provides:

✅ Continuous outbreak monitoring
✅ Automated data updates
✅ Reproducible ML workflow
✅ Real-time risk assessment
✅ Scalable public-health surveillance system
✅ Production-style AI engineering pipeline

---

## Built for

**KTT Fellows Week 1 Capstone Project**

**ViralWatch — AI-Powered Viral Haemorrhagic Fever Early-Warning System**

```

This is ready to paste directly into your repository `README.md`.
```
