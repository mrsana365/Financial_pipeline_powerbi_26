# Automated Financial Analytics Pipeline & Executive Power BI Dashboard

[![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=flat&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![Python 3.10+](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![Google Cloud](https://img.shields.io/badge/Google_Cloud-4285F4?style=flat&logo=googlecloud&logoColor=white)](https://cloud.google.com/)
[![Power Automate](https://img.shields.io/badge/Power_Automate-0066FF?style=flat&logo=microsoftpowerautomate&logoColor=white)](https://flow.microsoft.com/)

---

## 📌 Executive Summary & Business Problem
A financial survey firm processing nationwide US consumer financial data faced major operational bottlenecks:
- **High Inefficiency:** Daily intake of ~25 Excel files from distributed survey teams arriving at 3:00 PM had to be manually downloaded, consolidated, cleaned, and visualized for client presentation by 8:00 PM.
- **Inflated Operational Costs:** Due to repetitive manual data ingestion and high error rates, two additional data assistants were hired, increasing overhead by **$12,000/month ($144,000/year)** without eliminating turnaround delays.
- **Data Quality Issues:** Text anomalies, missing values disguised as strings (`"Data Missing"`), and unstandardized fields severely slowed down analytical accuracy.

### 💡 The Solution
Designed and deployed a zero-cost, end-to-end automated ETL pipeline and interactive Power BI executive dashboard:
1. Automated email segregation and cloud ingestion using **Outlook Rules** and **Microsoft Power Automate**.
2. Automated multi-source extraction, validation, and schema merging via a secure **GCP Service Account API** bridge integrated directly into **Python** scripts in Power BI.
3. Structured deep data transformations in **Power Query (M Language)** for single-pass data processing.
4. Engineered advanced **DAX measures** (handling nested iterators, context transitions, and non-numeric string anomalies) to deliver customer segmentation, credit risk insights, and an automated **Customer Lifetime Value (LTV)** promotional framework.

---

## 🏗️ Architecture & Data Pipeline Workflow

```
[25 Daily Survey Emails] 
       │
       ▼ (Keyword Filtering)
[Outlook Dedicated Folder] 
       │
       ▼ (Automated Flow)
[Power Automate Cloud Flow] ──> Saves raw attachments to Google Drive
                                         │
                                         ▼ (Authenticated Service Account API)
                               [Python Data Ingestion Script]
                                         │ (pd.concat & in-memory buffer)
                                         ▼
                               [Power Query / M Transformation]
                                         │ (Single-pass parsing & cleaning)
                                         ▼
                               [Power BI Data Model & DAX Measures]
                                         │
                                         ▼
                               [Executive Interactive Dashboard]
```

---

## 🛠️ Key Technical Implementations

### 1. Ingestion Automation (Python + GCP API)
- Connected to Google Drive v3 API using a least-privilege `drive.readonly` service account JSON credential.
- In-memory aggregation of heterogeneous spreadsheets (`.xlsx`, `.csv`, `.gsheet`) using `io.BytesIO` and `pandas.concat` without touching local disk space.

### 2. Robust Data Transformation (Power Query / M)
- **Age Standardization:** Handled text artifacts (`_`), negative ages, and outliers using median imputation (`List.Median`) scoped within valid ranges (13–95 years).
- **Salary Reconstruction:** Solved extensive null values in take-home pay by reverse-calculating net monthly salary from gross annual income assuming baseline statutory deductions:
  ```powerquery
  ([Annual Income] - ([Annual Income] * 0.10)) / 12
  ```
- **Credit History Conversion:** Parsed mixed-unit duration strings (`"28 Years 11 Months"`) into total months via delimited string slicing:
  ```powerquery
  if Text.Trim([Credit History Age]) = "" then 0 
  else (Number.FromText(Text.BeforeDelimiter([Credit History Age], "Year")) * 12) + 
       Number.FromText(Text.BetweenDelimiters([Credit History Age], "and", "Month"))
  ```
- **Delimited Loan Decomposition:** Normalized multi-valued loan entries per customer into granular analytical rows using delimiter splitting in single-pass evaluation.

### 3. Advanced DAX Engineering
- **Non-Destructive Error Handling:** Evaluated numeric measures over columns containing persistent business placeholders (`"Data Missing"`) without altering client source standards:
  ```dax
  Average Credit Inquiries = 
  AVERAGEX(
      FILTER(
          'Combine DF',
          NOT(ISERROR(VALUE('Combine DF'[Number of Credit Inquiries])))
      ),
      VALUE('Combine DF'[Number of Credit Inquiries])
  )
  ```
- **Customer Lifetime Value (LTV) Model:** Weighted multivariate scoring engine mapping credit tiers dynamically using `SWITCH` and `CALCULATE` within an iterator:
  $$\text{LTV} = (0.30 \times \text{Income}) - (0.15 \times \text{Delay}) + (0.40 \times \text{Credit Score}) + (0.075 \times \text{Invested}) + (0.075 \times \text{Balance})$$

---

## 📊 Business Insights Delivered

1. **Age vs. Credit Limit Elasticity:** Identified negligible baseline variance in automated credit limit adjustments across middle-age cohorts, highlighting opportunity for tailored tiering.
2. **Loan Demand Signals:** Isolated customer age brackets where average inquiries exceeded 7.5 (notably young adult demographics 14–19 and 21–24), pinpointing primary cross-selling targets for consumer loans.
3. **Credit Tier Behavior:** Established that *Low Spend Value Payments* dominate across all credit mix brackets, prompting targeted promotional tiers based on computed LTV scores.

---

## 🚀 How to Replicate Locally

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/your-username/financial-pipeline-powerbi.git](https://github.com/your-username/financial-pipeline-powerbi.git)
   cd financial-pipeline-powerbi
   ```
2. **Install Python dependencies:**
   ```bash
   pip install google-auth google-auth-oauthlib google-auth-httplib2 google-api-python-client pandas numpy
   ```
3. **Setup GCP Credentials:**
   - Create a Google Cloud project with Google Drive API enabled.
   - Generate a Service Account key (`credentials.json`) and share your Drive folder with the service account client email.
   - Update `SERVICE_ACCOUNT_FILE` and `FOLDER_ID` in `src/python/drive_combine_pipeline.py`.
4. **Open Dashboard:**
   - Open `dashboard/Financial_Analytics_Dashboard.pbix` in Microsoft Power BI Desktop.
   - Click **Refresh** to run transformations and update visuals.
