# AWS Healthcare Stroke Analytics Warehouse

> Cloud-based healthcare data warehouse on AWS (S3 · Glue · Athena) analysing 5,100+ stroke patient records, with a Power BI dashboard for clinical risk insights.
## Overview

A fully serverless healthcare analytics pipeline that ingests raw medical data, preprocesses it with distributed PySpark ETL, stores structured results in the cloud, queries via SQL, and visualises stroke risk insights in Power BI — with no server management required.

---

## Architecture
Kaggle CSV
↓
AWS S3 (raw/)
↓
AWS Glue Crawler → Data Catalog (schema detection)
↓
AWS Glue ETL Job (PySpark) → S3 (processed/stroke_clean.parquet)
↓
Amazon Athena → healthcare_db.stroke_clean_parquet
↓
Athena Views (Fact + Dimension + Analytics)
↓
Power BI Dashboard

---

## AWS Pipeline Steps

### Step 1 — Data Ingestion (S3)
- Raw CSV uploaded to `s3://bucket/raw/`
- Acts as the landing zone for incoming medical files

### Step 2 — ETL Preprocessing (AWS Glue)
**Glue Crawler:**
- Scans `raw/` folder, auto-detects schema
- Creates a Glue Data Catalog entry

**Glue ETL Job (PySpark):**
- Fills missing BMI values
- Converts column data types
- Replaces missing smoking status values
- Removes duplicate records
- Filters invalid age values
- Converts CSV → Parquet
- Output: `processed/stroke/stroke_clean.parquet`

### Step 3 — Data Warehouse (Amazon Athena)
- Creates `healthcare_db` database
- External table over processed Parquet file
- Serverless SQL queries — no infrastructure needed

### Step 4 — Fact & Dimension Views
- **Dimension table:** patient demographics
- **Fact table:** clinical measurements and stroke outcomes
- **Analytics view:** `v_stroke_analytics` — joined view for reporting

### Step 5 — Power BI Dashboard
- Exported analytics view to Power BI
- DAX measures and calculated columns built for clinical KPIs

---

## Power BI Dashboard

### DAX Measures
| Measure | Description |
|---------|-------------|
| Total Patients | COUNT of all patient records |
| Stroke Count | COUNTROWS filtered to stroke = 1 |
| Stroke Rate % | Stroke Count / Total Patients |
| Hypertension Rate % | % of patients with hypertension |
| Heart Disease Rate % | % of patients with heart disease |
| Average BMI | Mean BMI across all patients |
| Average Glucose | Mean avg_glucose_level |

### DAX Calculated Columns
- **Age Group:** 0–19 / 20–39 / 40–59 / 60–79 / 80+
- **Comorbidity:** None / Hypertension only / Heart disease only / Both conditions

### Visuals
- KPI cards: Patient Count, Stroke Count, Stroke Rate %, BMI, Glucose
- Stroke by Gender (donut chart)
- Stroke by Age Group (column chart)
- Stroke by Work Type (bar chart)
- Stroke by Smoking Status
- Stroke by Comorbidity
- Two scatter charts (BMI vs Glucose, coloured by stroke)
- Slicers: gender, smoking, work type, residence, age group

---

## Dataset

[Kaggle — Stroke Prediction Dataset](https://www.kaggle.com/datasets/fedesoriano/stroke-prediction-dataset)  
5,110 patient records | 12 features | Binary stroke outcome

| Feature | Description |
|---------|-------------|
| age | Patient age |
| hypertension | Hypertension flag (0/1) |
| heart_disease | Heart disease flag (0/1) |
| ever_married | Marital status |
| work_type | Employment category |
| Residence_type | Urban / Rural |
| avg_glucose_level | Average blood glucose |
| bmi | Body mass index |
| smoking_status | Smoking history |
| stroke | Target variable (0/1) |

---

## AWS Tools — Suitability

| Tool | Role |
|------|------|
| S3 | Scalable data lake for raw & processed files |
| Glue Crawler | Automated schema detection |
| Glue ETL | Distributed PySpark preprocessing |
| Parquet | Columnar format optimised for Athena queries |
| Athena | Serverless SQL — no infrastructure to manage |
| Power BI | Clinical dashboard for healthcare analysts |

---

## References

- Kaggle (2020). Stroke Prediction Dataset
- AWS Glue Developer Guide — https://docs.aws.amazon.com/glue/
- Amazon Athena User Guide — https://docs.aws.amazon.com/athena/
- Microsoft Power BI Documentation — https://learn.microsoft.com/en-us/power-bi/

---
