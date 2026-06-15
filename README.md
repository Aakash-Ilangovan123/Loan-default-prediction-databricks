# 🏦 Loan Default Prediction System
### Databricks Lakehouse Pipeline | Medallion Architecture | MLflow

![Databricks](https://img.shields.io/badge/Databricks-Serverless-FF3621?logo=databricks)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-Enabled-00ADD8)
![MLflow](https://img.shields.io/badge/MLflow-Tracked-0194E2)
![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python)

---

## 📌 Problem Statement
Banks lose billions annually to loan defaults. This project builds an 
end-to-end Lakehouse pipeline on Databricks to predict whether a loan 
applicant will default — enabling smarter, data-driven credit decisions.

---

## 🏗️ Architecture — Medallion Lakehouse
Raw CSV (307K rows)

↓

🥉 BRONZE LAYER

• Raw ingestion into Delta table

• Schema enforcement

• Ingestion metadata added

↓

🥈 SILVER LAYER

• Null handling (40% threshold drop)

• Data type fixes

• String cleaning & encoding

↓

🥇 GOLD LAYER

• 8 engineered features

• Risk segmentation

• Business aggregations

• Delta OPTIMIZE applied

↓

🤖 ML PIPELINE

• Random Forest Classifier

• Class weight balancing (1:10)

• MLflow experiment tracking

• Unity Catalog model registry

↓

📊 PREDICTIONS TABLE

• Batch inference results

• Default probability scores

---

## 📁 Project Structure

| Notebook | Layer | Description |
|---|---|---|
| `01_bronze_ingestion` | Bronze | Raw CSV → Delta table with metadata |
| `02_silver_cleaning` | Silver | Null handling, type fixing, encoding |
| `03_gold_features` | Gold | Feature engineering + risk segments |
| `04_ml_training` | ML | Train RandomForest + MLflow logging |
| `05_eda_business_insights` | Analytics | SQL insights + business findings |
| `06_orchestration` | Pipeline | Verification + data quality checks |

---

## 📊 Dataset
- **Source:** Home Credit Default Risk (Kaggle)
- **Size:** 307,511 applicants | 122 raw features
- **Target:** Binary (1 = Default, 0 = No Default)
- **Class Imbalance:** 91.9% No Default | 8.1% Default

---

## 🔧 Feature Engineering

| Feature | Description |
|---|---|
| `CREDIT_INCOME_RATIO` | Credit amount / Annual income |
| `ANNUITY_INCOME_RATIO` | Annuity / Annual income |
| `AGE_YEARS` | Applicant age in years |
| `EMPLOYMENT_YEARS` | Years employed |
| `IS_UNEMPLOYED` | Flag for anomalous employment |
| `EXT_SOURCE_MEAN` | Average external credit score |
| `TOTAL_DOCS_SUBMITTED` | Count of documents provided |
| `SOCIAL_CIRCLE_DEFAULT_RATE` | Peer default risk |

---

## 🤖 Model Results

| Metric | Score |
|---|---|
| ROC-AUC | **0.7345** |
| Accuracy | - |
| F1 Score | - |
| Precision | - |
| Recall | - |

> Fill in your actual scores from Cell 4 output

**Top Predictive Features:**
1. EXT_SOURCE_MEAN (external credit score)
2. AMT_CREDIT (loan amount)
3. CREDIT_INCOME_RATIO
4. AGE_YEARS

---

## 💡 Key Business Insights

- 📌 **Very High Risk** segment has the highest default rate
- 📌 **Younger applicants** (Under 25) default more than older groups  
- 📌 **Lower education** correlates with higher default risk
- 📌 **Cash loans** default more than revolving loans
- 📌 **Unemployed applicants** show significantly higher default rates

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Databricks Serverless | Compute & notebook environment |
| Apache Spark (PySpark) | Distributed data processing |
| Delta Lake | ACID transactions + time travel |
| MLflow | Experiment tracking + model registry |
| Unity Catalog | Governed data + model storage |
| Scikit-learn | ML model training |
| Python 3.10 | Primary language |

---

## 🚀 How to Run

1. Create a free **Databricks Community Edition** account
2. Upload `application_train.csv` to your Volume
3. Import all notebooks into your Workspace
4. Run notebooks **in order: 01 → 02 → 03 → 04 → 05 → 06**
5. View MLflow experiments under **Experiments** tab

---

## 👨‍💻 Author
**Aakash** — AI & Data Science Engineer  
🔗 [LinkedIn](www.linkedin.com/in/aakashilangovan37) | 
⭐ Star this repo if you found it useful!
