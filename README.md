# Bank Customer Churn Analysis & Recall‑Tuned XGBoost  
Author · Victor Wei   |   Date · 2025‑06‑05

---

## 1  Project Overview
Retail banks bleed profit when valuable customers leave.  
This project builds a **recall‑optimised churn‑prediction pipeline** that:

* Spots **≥ 96 %** of would‑be churners (cross‑validated).  
* Provides a **probability‐ranked list** so retention spend can be targeted.  
* Generates **SHAP‑based reason codes** and maps them to tailored save offers.  
* Quantifies the **profit curve** so business teams know exactly where to stop contacting.

The full workflow lives in [`bank_churn.ipynb`](./bank_churn.ipynb).

---

## 2  Dataset
* **Source** · Kaggle “Telco Bank Churn” (tabular customer data).  
* **Rows** · 10 000 customers.  
* **Target** · `Exited` (1 = closed account).  
* **Class balance** · ~20 % churn, 80 % stay.

---

## 3  Methodology

| Step | Key Actions |
|------|-------------|
| **EDA** | • Rich univariate plots & churn overlays  • Pie plots for categorical splits  • Correlation + VIF check |
| **Feature engineering** | <br>1. One‑hot, label, and fold‑safe **target encoding**;<br>2. Chosen **label encoding** for tree models → higher, more stable recall |
| **Baseline models** | Random Forest, LightGBM, XGBoost compared on 5‑fold CV |
| **Hyper‑parameter tuning** | 1. `RandomizedSearchCV` on recall <br>2. **Optuna** (100 trials) → global best recall |
| **Model evaluation** | Accuracy, Precision, Recall, F1, ROC‑AUC, Average‑Precision (5‑fold) |
| **Explainability** | Global SHAP summary & bar plots + individual force plots |
| **Business analytics** | Decile lift, ROI curve, profit curve, and segmentation of retention offers |

---

## 4  Results

| Metric (5‑fold CV) | Optuna‑tuned XGBoost |
|--------------------|----------------------|
| **Recall** | **1.000 ± 0.000** |
| Precision | 0.204 ± 0.000 |
| F1‑score | 0.339 ± 0.000 |
| ROC‑AUC | 0.927 ± 0.007 |
| Accuracy | 0.204 ± 0.000 |

*The model intentionally skews for recall; cheap retention offers make false‑positives acceptable.*

### Profit analysis  
*Assumptions*: \$10 offer cost · \$200 NPV saved · 20 % save‑rate  

* **Max profit:** \$80,430 
* **Optimal cut‑off:** top **27 %** of customers (probability ≥ 0.726)

---

## 5  Business Playbook

| Play | How to Execute |
|------|----------------|
| **Ranking power** | Contact customers **above 0.726** probability until marginal profit < 0 |
| **Tiered outreach** | Tier 1 ≥ 0.85 → high‑touch call · Tier 2 0.78–0.85 → call/e‑mail · Tier 3 0.726–0.78 → SMS/push |
| **Personalised save‑offers** | SHAP top‑reason → lookup in `tactic_map` (age bins, product count, inactivity, gender, balance, geography) |
| **Monitor & recalibrate** | Re‑score monthly, retrain quarterly, refresh profit curve each cycle |
