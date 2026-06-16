# 🏦 Loan Default Prediction System
### End-to-End Databricks Lakehouse Pipeline | Medallion Architecture | MLflow | Delta Lake

![Databricks](https://img.shields.io/badge/Databricks-Serverless-FF3621?logo=databricks)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-Enabled-00ADD8)
![MLflow](https://img.shields.io/badge/MLflow-Tracked-0194E2)
![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python)
![PySpark](https://img.shields.io/badge/PySpark-3.5-E25A1C?logo=apachespark)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-F7931E?logo=scikitlearn)

> **Codebasics Data Engineering Challenge** — Built using Databricks Serverless

---

## 📌 Problem Statement

Banks lose billions annually to loan defaults. This project builds a production-grade, end-to-end **Lakehouse pipeline on Databricks** to predict whether a loan applicant will default — enabling smarter, data-driven credit risk decisions.

**Business Impact:** Early identification of high-risk applicants reduces Non-Performing Assets (NPAs) and improves loan portfolio quality.

---

## 🏗️ Architecture — Medallion Lakehouse

```
┌─────────────────────────────────────────────────────────┐
│              RAW DATA SOURCE                            │
│   Home Credit CSV · 307,511 rows · 122 features        │
└───────────────────────┬─────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────┐
│  🥉  BRONZE LAYER  (01_bronze_ingestion)                │
│  • Raw CSV ingested as-is into Delta table              │
│  • Schema enforcement via inferSchema                   │
│  • Metadata columns added (_ingested_at, _source_file)  │
│  • 307,511 rows · 125 cols (122 + 3 metadata)          │
└───────────────────────┬─────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────┐
│  🥈  SILVER LAYER  (02_silver_cleaning)                 │
│  • Dropped columns with >40% null values                │
│  • Fixed data types (Y/N → 1/0, M/F → 1/0)            │
│  • Filled numeric nulls with 0                          │
│  • Filled categorical nulls with "UNKNOWN"              │
│  • 307,511 rows · 75 cols (clean, typed, no nulls)     │
└───────────────────────┬─────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────┐
│  🥇  GOLD LAYER  (03_gold_features)                     │
│  • 8 engineered financial & risk features               │
│  • Risk segmentation (LOW / MEDIUM / HIGH / VERY HIGH)  │
│  • Business aggregations for analytics                  │
│  • Delta OPTIMIZE applied for query performance         │
│  • 307,511 rows · 83 cols                              │
└───────────────────────┬─────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────┐
│  🤖  ML PIPELINE  (04_ml_training)                      │
│  • Random Forest Classifier                             │
│  • Class weight balancing {0:1, 1:10} for imbalance    │
│  • MLflow experiment tracking + metric logging          │
│  • Model registered in Unity Catalog                    │
│  • ROC-AUC: 0.7345                                     │
└───────────────────────┬─────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────┐
│  📊  PREDICTIONS TABLE  (04_ml_training Cell 7)         │
│  • Batch inference on 20% test set (61,502 rows)        │
│  • Columns: PREDICTED_DEFAULT + DEFAULT_PROBABILITY     │
│  • Saved back to Gold Delta table                       │
└─────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

| # | Notebook | Layer | Description |
|---|---|---|---|
| 01 | `01_bronze_ingestion` | 🥉 Bronze | Raw CSV → Delta table with ingestion metadata |
| 02 | `02_silver_cleaning` | 🥈 Silver | Null handling, type fixing, binary encoding |
| 03 | `03_gold_features` | 🥇 Gold | Feature engineering + risk segmentation |
| 04 | `04_ml_training` | 🤖 ML | Train RandomForest, MLflow logging, model registry |
| 05 | `05_eda_business_insights` | 📊 Analytics | SQL-based business insights + findings |
| 06 | `06_orchestration` | ⚙️ Pipeline | End-to-end verification + data quality checks |

---

## 📊 Dataset

| Property | Value |
|---|---|
| Source | [Home Credit Default Risk — Kaggle](https://www.kaggle.com/competitions/home-credit-default-risk) |
| Size | 307,511 applicants |
| Raw Features | 122 columns |
| Target Column | `TARGET` (1 = Default, 0 = No Default) |
| Class Distribution | 91.9% No Default · 8.1% Default |
| Imbalance Handling | Class weight `{0: 1, 1: 10}` in Random Forest |

---

## 🔧 Feature Engineering (Gold Layer)

| Feature | Formula / Logic | Business Meaning |
|---|---|---|
| `CREDIT_INCOME_RATIO` | AMT_CREDIT / AMT_INCOME_TOTAL | Debt burden relative to income |
| `ANNUITY_INCOME_RATIO` | AMT_ANNUITY / AMT_INCOME_TOTAL | Monthly repayment burden |
| `CREDIT_GOODS_RATIO` | AMT_CREDIT / AMT_GOODS_PRICE | Loan-to-value ratio |
| `INCOME_PER_PERSON` | AMT_INCOME_TOTAL / CNT_FAM_MEMBERS | Per-capita family income |
| `AGE_YEARS` | -DAYS_BIRTH / 365 | Applicant age in years |
| `EMPLOYMENT_YEARS` | -DAYS_EMPLOYED / 365 | Years at current job |
| `IS_UNEMPLOYED` | DAYS_EMPLOYED > 0 → 1 | Flags anomalous employment values |
| `EXT_SOURCE_MEAN` | (EXT_SOURCE_2 + EXT_SOURCE_3) / 2 | Average external credit score |
| `TOTAL_DOCS_SUBMITTED` | SUM of all FLAG_DOCUMENT_* | Document completeness |
| `SOCIAL_CIRCLE_DEFAULT_RATE` | DEF / OBS social circle ratio | Peer group default risk |

---

## 🤖 Model Results

| Metric | Score |
|---|---|
| **ROC-AUC** | **0.7345** |
| Accuracy | *(from Cell 4 output)* |
| F1 Score | *(from Cell 4 output)* |
| Precision | *(from Cell 4 output)* |
| Recall | *(from Cell 4 output)* |

**Algorithm:** Random Forest Classifier (n_estimators=100, max_depth=8)

**Top Predictive Features:**
1. `EXT_SOURCE_MEAN` — External credit score average
2. `AMT_CREDIT` — Total loan amount
3. `CREDIT_INCOME_RATIO` — Debt-to-income ratio
4. `AGE_YEARS` — Applicant age
5. `EMPLOYMENT_YEARS` — Job stability

---

## 💡 Key Business Insights

| # | Finding |
|---|---|
| 1 | **Very High Risk** segment applicants default at the highest rate |
| 2 | **Younger applicants (Under 25)** show significantly higher default rates |
| 3 | **Lower education level** strongly correlates with higher default risk |
| 4 | **Cash loans** default more frequently than revolving loans |
| 5 | **Unemployed applicants** (anomalous DAYS_EMPLOYED > 0) are high-risk |
| 6 | Dataset is heavily imbalanced — 8.1% default rate requires class weighting |

---

## 🛠️ Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| Compute | Databricks Serverless | Notebook execution environment |
| Processing | Apache Spark (PySpark 3.5) | Distributed data transformation |
| Storage | Delta Lake (Unity Catalog) | ACID transactions + time travel |
| ML Tracking | MLflow (Databricks managed) | Experiment logging + model registry |
| ML Library | Scikit-learn | Random Forest training |
| Language | Python 3.10 | Primary development language |
| Data Source | Kaggle (Home Credit) | Raw loan application dataset |

---

## 🔍 Delta Lake Features Used

- **ACID Transactions** — Safe writes with `overwriteSchema=true`
- **Schema Enforcement** — Type-safe Delta tables at every layer
- **Time Travel** — `DESCRIBE HISTORY` on all tables
- **OPTIMIZE** — Compaction for query performance on Gold table
- **Unity Catalog** — Governed namespace `workspace.default.*`

---

## 🚀 How to Run

### Prerequisites
- Databricks account (Community Edition or Serverless Starter)
- Kaggle account to download `application_train.csv`

### Steps

**1. Upload Dataset**
```
Databricks → Catalog → Add Data → Upload to Volume
Path: /Volumes/workspace/default/loan_project/application_train.csv
```

**2. Import Notebooks**
```
Workspace → Create Folder: loan_default_project
Import each .ipynb file into the folder
```

**3. Run in Order**
```
01_bronze_ingestion       ← Start here
02_silver_cleaning
03_gold_features
04_ml_training
05_eda_business_insights
06_orchestration          ← Final verification
```

**4. View MLflow Results**
```
Left sidebar → Experiments → /loan_default_prediction
```

---

## 📈 Pipeline Metrics Summary

| Layer | Rows | Columns | Key Action |
|---|---|---|---|
| Raw CSV | 307,511 | 122 | Source file |
| Bronze | 307,511 | 125 | +3 metadata columns |
| Silver | 307,511 | 75 | -50 high-null + cleaned |
| Gold | 307,511 | 83 | +8 engineered features |
| Predictions | ~61,502 | 35 | 20% test set scored |

---

## 👨‍💻 Author

**Aakash** — AI & Data Science Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?logo=linkedin)](https://www.linkedin.com/in/aakashilangovan37)

> ⭐ Star this repo if you found it useful!
> 
> Built as part of the **Codebasics Data Engineering Challenge**
