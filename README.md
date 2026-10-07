# BRSR-Auditing
### Automated BRSR ESG Accounting & Anomaly Detection Pipeline

An enterprise-grade Python framework designed for automated **Business Responsibility and Sustainability Reporting (BRSR)** data processing, **ESG risk feature engineering**, and **anomaly detection**.

This system ingests raw corporate ESG disclosures, resolves structural column duplications, performs peer-group hierarchy imputations, builds normalized risk/intensity metrics, and isolates forensic audit flags using **Isolation Forest** models scaled with **RobustScaler**.

---

## 🏗️ System Architecture

```
                                  RAW BRSR DATA DISCLOSURES
                                              │
                                              ▼
                             ┌─────────────────────────────────┐
                             │   Type Normalization & Safety   │
                             │   - Deduplicate Columns         │
                             │   - Numeric Conversion & Bounds │
                             └────────────────┬────────────────┘
                                              │
                                              ▼
                             ┌─────────────────────────────────┐
                             │     Hierarchical Imputation     │
                             │  1. Optimized Group Median      │
                             │  2. Global Metric Median        │
                             │  3. Zero Fill Fallback          │
                             └────────────────┬────────────────┘
                                              │
                                              ▼
                             ┌─────────────────────────────────┐
                             │       Feature Engineering       │
                             │  - Bounded Deficit Scores [0,1] │
                             │  - Unbounded Financial Gaps     │
                             │  - Activity & Incident Rates    │
                             └────────────────┬────────────────┘
                                              │
                                              ▼
                             ┌─────────────────────────────────┐
                             │   IQR Scaling & Outlier Guard   │
                             │   - RobustScaler Transformation │
                             │   - Anchor Tracking Integration │
                             └────────────────┬────────────────┘
                                              │
                                              ▼
                             ┌─────────────────────────────────┐
                             │  Isolation Forest Audit Engine  │
                             │  - Anomaly Scoring (-1 vs 1)    │
                             └─────────────────────────────────┘

```

---

## ✨ Key Features

* **Multi-Stage Peer-Group Imputation:** Fills missing BRSR data by prioritizing industry group peer medians (`Optimized_Group`) before falling back to global portfolio medians, preventing cross-sector distortion.
* **Unified Audit Feature Framing:**
* **Bounded Risk Metrics $[0, 1]$:** Inverts positive rates into risk deficits (e.g., $1 - \text{Training Rate}$) where higher scores consistently denote higher risk exposure across human rights, diversity, and governance.
* **Unbounded Pay Inequity Ratios:** Calculates extreme compensation disparities comparing Board, Key Management Personnel (KMP), and Non-Executive staff against frontline worker medians.
* **Revenue-Normalized Intensities:** Scales physical metrics (workplace injuries, disciplinary actions, water/energy consumption) against Turnover to evaluate operational efficiency independent of enterprise scale.


* **Outlier-Resistant Preprocessing:** Leverages `RobustScaler` (centering on the median and scaling via Interquartile Range) to prevent extreme corporate outliers (e.g., a $2000\times$ pay gap) from collapsing standard normal distributions.
* **Unsupervised Anomaly Auditing:** Implements `IsolationForest` across fiscal years (`fyear`) to flag suspicious disclosures, potential greenwashing, or severe operational risks.


## 🛠️ Audit Score Interpretation

The Isolation Forest outputs two primary metrics used in reporting dashboards:

1. **`audit_anomaly_flag`**:
* `1`: Normal Disclosure Pattern (In-line with peer group bounds).
* `-1`: **Audit Flag / Anomaly Detected** (Significant deviation in risk gaps, pay ratios, or reporting consistency).


2. **`anomaly_score`**: Continuous decision boundary score. Highly negative values indicate extreme anomalies requiring manual forensic auditing.
