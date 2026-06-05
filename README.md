# 💳 Payment Fraud Intelligence Dashboard

> A 3-page Power BI dashboard analyzing **284,807 credit card transactions** to detect fraud patterns, forecast risk volume, and translate analysis into actionable recommendations for fraud teams.

**Tech Stack:** Power BI · Python (Pandas, scikit-learn, Prophet) · DAX · Star Schema Modeling

---

## 🎯 The Business Problem

Payment companies (Paymob, Fawry, Stripe) face an asymmetric problem: catch fraud in milliseconds across millions of transactions, or absorb the losses. Real fraud teams need three things from their analytics:

1. **Detect** anomalies in real time
2. **Forecast** risk volume for capacity planning
3. **Explain** WHY each flagged transaction was suspicious

This project simulates that end-to-end workflow using a public dataset, demonstrating the full data science pipeline from raw transactions → ML predictions → executive dashboard.

---

## 📊 The Dashboard (3 Pages)

### Page 1 — Executive Overview
**The question it answers:** *What's happening with fraud right now?*

- 5 headline KPIs (Total Frauds, Volume, Rate %, Avg Amount, High Risk Count)
- Fraud Pattern Across the Day (line chart)
- Fraud Distribution by Amount Bucket
- Top Predictive Features (Random Forest importance)

### Page 2 — Risk Patterns Deep Dive
**The question it answers:** *When and why does fraud happen?*

- 3 insight-driven KPIs (not just numbers — actual findings)
- **Hour × Amount Bucket heatmap** — concentration in 2D
- **Volume vs Fraud Rate combo chart** — proves the inverse relationship
- Fraud Distribution by Amount
- Hours Ranked by Fraud Risk Multiplier (the 9.9x bar)

### Page 3 — Forecast & Action
**The question it answers:** *What's coming and what should we do?*

- 30-Day Forecast KPIs
- Historical Hourly Pattern (forecast basis)
- **Recommended Actions table** — translates analysis into operational decisions
- Color-coded by risk level (Critical / Elevated / Normal / Low)

---

## 🔍 Key Findings

| Finding | The Data | The Business Implication |
|---|---|---|
| **Fraud is 9.9× more likely at 2 AM** | Hour 2 has 1.71% fraud rate vs 0.17% daily average | Deploy extra monitoring during low-volume hours |
| **73.6% of frauds are under €100** | Median fraud is €9.25 vs €22 for legitimate transactions | Fraudsters deliberately stay small to avoid detection thresholds |
| **5 features drive 63% of predictions** | V14, V10, V17, V4, V12 account for the majority of model signal | Feature engineering matters more than algorithm choice |
| **Low volume → High risk rate** | Inverse correlation between transaction volume and fraud rate by hour | Fraudsters strike when fewer eyes are watching |

---

## 🛠️ Technical Implementation

### Data Pipeline (Python)
```
Raw CSV (284,807 rows) 
   ↓ Pandas
Feature Engineering (Hour, AmountBucket, IsHighRiskHour)
   ↓ scikit-learn
Isolation Forest (unsupervised anomaly detection)
   ↓
Random Forest Classifier (supervised, class-weighted)
   ↓ Prophet → fallback to pattern-based
30-Day Forecast
   ↓
Clean CSVs ready for Power BI
```

### Models Used
- **Isolation Forest** — unsupervised baseline for anomaly detection
- **Random Forest Classifier** — supervised with `class_weight='balanced'` for the 0.17% imbalance
- **Pattern-based forecast** — historical hourly distribution projected forward (chosen over Prophet after 2-day data limitation made Prophet extrapolations unreliable)

### Model Performance (Honest Numbers)
- **Isolation Forest:** 25% precision, 29% recall, 27% F1
- **Random Forest:** [Used as supervised baseline with `class_weight='balanced'` to handle the extreme imbalance]
- Used **precision and recall** instead of accuracy — accuracy is meaningless on 0.17% positive class

### Power BI Architecture
- **5 imported tables** (transactions, hourly summary, amount summary, feature importances, forecast)
- **12 DAX measures** organized in a Calculations folder
- **4 slicers** (Day, Hour range, Risk Category, Amount Bucket) in a dark sidebar
- **Consistent design system** — red (`#C00000`) for risk, blue (`#1F4E79`) for primary, gray (`#595959`) for neutral

---

## 🧠 What I Learned

1. **Accuracy is a lie on imbalanced data.** A model predicting "no fraud" for everything gets 99.83% accuracy and is useless. Precision, recall, and F1 are the real metrics.

2. **Forecasts need data.** Prophet needs 2-4 weeks of history minimum. With 2 days, it extrapolated negative fraud counts. Lesson: pick model complexity based on data availability, not library prestige.

3. **The "Why?" matters more than the "What?"** A dashboard that shows numbers is okay. A dashboard that explains causes (low volume → high fraud rate) is useful. A dashboard that recommends actions is what fraud teams actually want.

4. **Unsupervised first, supervised second.** Isolation Forest doesn't need labels — useful when fraud labels don't exist yet. Random Forest needs labels but performs better. Real fintechs use both: unsupervised catches new fraud patterns, supervised catches known ones.

---

## 📂 Repository Structure

```
fintech-fraud-intelligence-dashboard/
├── README.md                          ← You're here
├── notebook/
│   └── 01_data_exploration.ipynb     ← Complete analysis notebook
├── data/
│   ├── creditcard.csv                ← Source dataset (download from Kaggle, not committed)
│   ├── transactions_for_powerbi.csv  ← Cleaned + engineered (60 MB)
│   ├── hourly_summary.csv            ← Pre-aggregated hourly stats
│   ├── amount_summary.csv            ← Amount bucket distribution
│   ├── feature_importances.csv       ← Random Forest top features
│   └── fraud_forecast.csv            ← 30-day forecast output
├── dashboard/
│   └── Fraud_Intelligence_Dashboard.pbix  ← Power BI file
└── screenshots/
    ├── page1-executive-overview.png
    ├── page2-risk-patterns.png
    ├── page3-forecast-action.png
    ├── fraud_by_hour.png
    ├── amount_distribution.png
    ├── feature_importance.png
    └── fraud_forecast.png
```

---

## 🚀 How to Reproduce

1. **Download the dataset** from [Kaggle: Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) and place `creditcard.csv` in `data/`

2. **Set up Python environment:**
   ```bash
   python -m venv fraud-env
   .\fraud-env\Scripts\activate    # Windows
   pip install pandas numpy scikit-learn matplotlib seaborn jupyter prophet
   ```

3. **Run the notebook** end-to-end:
   ```bash
   jupyter notebook notebook/01_data_exploration.ipynb
   ```

4. **Open Power BI Desktop** and load `dashboard/Fraud_Intelligence_Dashboard.pbix`. Refresh data sources to point to the regenerated CSVs.

---

## 📌 Why This Project

I noticed every fintech job description I applied to assumed candidates had domain experience I didn't have yet. So I built the experience myself — a project that demonstrates the actual workflow of fraud analytics at a payments company.

The goal wasn't to publish another "Credit Card Fraud" Kaggle notebook with high model accuracy. It was to build something that **looks like real fintech BI work**: an executive dashboard that translates 284,807 raw transactions into 4 specific recommended actions for a fraud team's day.

---

## 👤 About Me

**Youssef Sherif** — Data Analyst | BI Developer | Cairo, Egypt

Recent CS graduate (HTI, 2025) with hands-on experience building Power BI dashboards, automated reporting workflows, and AI-integrated analytics for freelance clients since September 2024.

- 🌐 **LinkedIn:** [linkedin.com/in/youssef-sherif-/](https://www.linkedin.com/in/youssef-sherif-/)
- 💻 **GitHub:** [github.com/youssef-star62](https://github.com/youssef-star62)
- 📧 **Email:** youssefsherif20032003@gmail.com

---

## 📝 License

This project is open source under the MIT License. The dataset is from [Andrea Dal Pozzolo et al., ULB Machine Learning Group](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) and is used under its Open Database License.
