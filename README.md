# 🏥 Healthcare Analytics Case Study

## 📘 Introduction
A healthcare analytics consulting firm requested an analysis to help hospitals use data to **improve patient care and reduce costs**.  
The insights from this project support better operational planning and data-driven healthcare decisions.

---

## 🎯 Business Task (Ask)
**Objective:**  
Analyze patient encounters, costs, coverage, and behavior trends to identify improvement opportunities in care and operations.

**Key Stakeholders:**  
- Hospital management  
- Healthcare analysts  
- Financial and operational planning teams  

---

## 🧩 Data Collection and Preparation

**Data Source:**  
Public Fitbit activity data from a Bellabeat competitor — [Kaggle Dataset](https://www.kaggle.com/datasets/arashnic/fitbit)

**Storage & Environment:**  
Data was uploaded and stored in **Google BigQuery**.

**Data Format:**  
Data is in **long format**, meaning each row represents one variable observation with multiple identifying columns.  
No unpivoting was required prior to analysis.

**Tools Used:**
- **BigQuery** – for data preparation and transformation  
- **Tableau** – for visualization and insights  

---

## 🧠 Data Reliability (ROCCC Framework)
To ensure that the data was **complete, consistent, accurate, and reliable**, the following validation steps were performed before analysis.

### ✅ Data Integrity Checks
- **Duplicate check:**  
  Verified no duplicate patient names in the `patients` table (assumed unique identifiers).  
- **Missing values:**  
  Identified and handled NULLs across key tables.  
- **Outliers:**  
  Example – `total_claim_cost` should never be negative.  
- **Totals cross-check:**  
  Compared aggregated totals against expectations or historical patterns (where available).

---

## 🗂️ Data Management and Standardization
The raw data, initially spread across multiple CSV files with varying granularities (day, hour, minute), was imported into a **secure Google Cloud environment**.

### Key Data Preparation Steps:
1. **File Standardization:**  
   Files renamed using a standardized format  
   ```
   YYYYMM_DataType_Granularity.csv
   ```
2. **Data Cleaning & Unification:**  
   All cleaning, joining, and aggregation were done using **SQL in BigQuery** for scalability.
3. **Validation:**  
   Ensured data completeness and consistency before analysis.

---

## 📊 Data Analysis

### 🔹 Encounter Analysis

#### 1. Encounter Volume & Trends
## 🧾 Example SQL Snippet
```sql
SELECT 
  EXTRACT(YEAR FROM START) AS yr,
  EXTRACT(MONTH FROM START) AS mo,
  count(Id)
FROM `maven_db.encounters`
GROUP BY 1,2
ORDER BY 1,2;

🖼️ *Placeholder for Tableau Chart 1 – Encounter Volume by Year*  
![Encounter Volume Chart](images/encounter_volume_chart.png)

#### 2. Cost & Coverage Insights
- Percentage of encounters with **zero payer coverage**
- **Top 10 most frequent procedures** and their **average base cost**
- **Average total claim cost** per payer

🖼️ *Placeholder for Tableau Chart 2 – Cost & Coverage Dashboard*  
![Cost and Coverage Dashboard](images/cost_coverage_dashboard.png)

---

### 🔹 Length of Stay (LOS) Analysis
Calculated **average LOS (hours)** by encounter class (ambulatory, inpatient, outpatient), grouped by **patient age and gender**.

#### Key Findings:
- **Males aged 31–40**: Highest average LOS in ambulatory encounters (~449 hrs)
- **Males aged 41–50**: Also elevated ambulatory LOS (~230 hrs)
- **Females aged 31–40**: Much lower ambulatory LOS (~96 hrs)
- **Females aged 51–100+**: Moderate inpatient LOS (~25–84 hrs)
- Overall: **Males 31–40** are primary drivers of high ambulatory LOS.

🖼️ *Placeholder for Tableau Chart 3 – LOS by Age & Gender*  
![LOS by Age Gender Chart](images/los_by_age_gender.png)

---

### 🔹 Readmissions & Follow-Up Visits
- **Goal:** Identify how many patients were readmitted within 30 days of a prior encounter.
- **Method:**  
  Used SQL **window functions** (`LEAD`) to calculate the interval between two admission dates.
- **Follow-up Analysis:**  
  - Created a subquery to count total 30-day readmissions  
  - Used filters to identify specific patients with multiple readmissions

🖼️ *Placeholder for Tableau Chart 4 – Readmission Trends*  
![Readmission Trends Chart](images/readmission_trends.png)

---

## ⚙️ Tools & Technologies
| Tool | Purpose |
|------|----------|
| **Google BigQuery** | Data storage, cleaning, and SQL transformations |
| **Tableau** | Visualization and dashboarding |
| **SQL** | Data joins, aggregations, and KPI logic |

---

## 🧾 Example SQL Snippet
```sql
SELECT 
  patient_id,
  encounter_class,
  AVG(length_of_stay_hours) AS avg_los
FROM `healthcare.encounters`
GROUP BY patient_id, encounter_class
ORDER BY avg_los DESC;
```

---

## 📈 Key Insights Summary
- Encounter trends reveal age and gender patterns in healthcare utilization.
- Coverage analysis highlights gaps in payer participation.
- High ambulatory LOS for males aged 31–40 suggests potential inefficiencies or data anomalies.
- Readmission tracking supports hospital care improvement planning.

---

## 🏁 Conclusion
This case study demonstrates how **data engineering (BigQuery)** and **data visualization (Tableau)** combine to deliver actionable insights for healthcare providers.  
By ensuring data integrity and applying SQL-based analytics, hospitals can better monitor patient behavior, reduce costs, and enhance quality of care.

---

### 👩‍💻 Author
**Analyst:** *[Your Name]*  
**Role:** Senior Financial / Data Analyst  
**Tools:** SQL | BigQuery | Tableau  
**Repository Type:** Case Study Portfolio  

