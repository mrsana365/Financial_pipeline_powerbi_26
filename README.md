# 📊 Mail2Dash: Automated Financial Data Pipeline & Dashboard

![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)
![Power BI](https://img.shields.io/badge/Power_BI-Advanced-yellow.svg)
![Power Automate](https://img.shields.io/badge/Power_Automate-Automation-blue.svg)
![GCP](https://img.shields.io/badge/Google_Cloud-API-orange.svg)
![Pandas](https://img.shields.io/badge/Pandas-Data_Manipulation-green.svg)

## 📖 Executive Summary
**Mail2Dash** is an end-to-end business automation solution designed to streamline the daily ingestion, transformation, and visualization of financial survey data for a US-based government client. 

The previous manual workflow was time-consuming, error-prone, and required two additional hires ($12,000/month overhead). This project successfully **automated 100% of the pipeline**, eliminating manual data handling, ensuring zero-error reliability, and consistently meeting the strict **8:00 PM SLA** for dashboard delivery.

---

## 🏗️ Data Pipeline Architecture

The pipeline utilizes a "Zero-Cost" integration stack to bridge separate ecosystems (Microsoft, Google, and Power BI) without requiring premium enterprise licenses.

```mermaid
graph LR
    A[Outlook Rules] -->|Auto-Move Emails| B(Power Automate)
    B -->|Download Attachments| C{Google Drive}
    C -->|OAuth 2.0 API| D[Python Engine]
    D -->|Merge & Clean| E[Power BI Desktop]
    E -->|Publish| F[Power BI Service]
    F -->|Scheduled Refresh| G((Stakeholders))
