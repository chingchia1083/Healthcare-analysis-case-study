# 🏥 Healthcare Analytics Case Study

## 📘 Introduction
This project was completed as part of a healthcare analytics engagement.  
The goal was to provide **data-driven insights** that help hospitals **improve patient care** and **reduce operational costs**.

---

## 🎯 Business Objective
**Task:** Analyze patient encounters, costs, coverage, and behavioral trends to support hospital planning and improve care outcomes.

**Key Stakeholders:**  
- Hospital Operations Teams  
- Healthcare Administrators  
- Data Strategy & Analytics Team  

---

## 🧩 Data Collection & Preparation

**Data Source:**  
[Data Drills – Maven Analytics](https://mavenanalytics.io/data-drills)

**Storage:**  
Data was imported and stored in **Google BigQuery**.

**Data Format:**  
The dataset is organized in **long format**, where each row represents a single observation with columns identifying variables or categories. This structure allows for efficient analysis without additional unpivoting.

**Tools Used:**  
- **BigQuery:** Data cleaning, validation, transformation  
- **Power BI:** Visualization and dashboard creation  

---

## ✅ Data Integrity & Quality Checks
Before analysis, the dataset was validated for completeness, consistency, and reliability.

### 1. Check for Duplicate Records  
To confirm each patient has a unique name:

```sql
SELECT
  Last,
  First,
  COUNT(*) AS row_count
FROM `maven_db.patients`
GROUP BY 1, 2
HAVING COUNT(*) > 1;
```

### 2. Check for Missing Values
Identify any `NULL` values in key fields:

```sql
SELECT
  COUNTIF(Patient IS NULL) AS null_patient,
  COUNTIF(Organization IS NULL) AS null_organization,
  COUNTIF(Payer IS NULL) AS null_payer
FROM `maven_db.patients`;
```

### 3. Check for Outliers
Ensure claim costs are valid (non-negative):

```sql
SELECT *
FROM `maven_db.patients`
WHERE TOTAL_CLAIM_COST < 0;
```

### 4. Validate Totals
Summarize total claim costs for comparison:

```sql
SELECT SUM(TOTAL_CLAIM_COST) AS total_claimed_cost
FROM `maven_db.patients`;
```

---

## 📊 Data Analysis

### 🔹 Encounter Analysis

#### Encounter Volume & Trends
Encounters per month, quarter, and year:

```sql
SELECT
  EXTRACT(YEAR FROM START) AS yr,
  EXTRACT(MONTH FROM START) AS mo,
  COUNT(Id)
FROM `maven_db.encounters`
GROUP BY 1, 2
ORDER BY 1, 2;
```

#### Encounter Class Distribution
Percentage of encounters by class (ambulatory, outpatient, wellness, urgent care, emergency, inpatient):

```sql
SELECT 
  EXTRACT(YEAR FROM START) AS year,
  COUNT(CASE WHEN ENCOUNTERCLASS = 'ambulatory' THEN Id END) AS ambulatory,
  COUNT(CASE WHEN ENCOUNTERCLASS = 'outpatient' THEN Id END) AS outpatient,
  COUNT(CASE WHEN ENCOUNTERCLASS = 'wellness' THEN Id END) AS wellness,
  COUNT(CASE WHEN ENCOUNTERCLASS = 'urgentcare' THEN Id END) AS urgentcare,
  COUNT(CASE WHEN ENCOUNTERCLASS = 'emergency' THEN Id END) AS emergency,
  COUNT(CASE WHEN ENCOUNTERCLASS = 'inpatient' THEN Id END) AS inpatient,
  COUNT(*) AS total_encounters
FROM `maven_db.encounters`
GROUP BY 1
ORDER BY 1;
```

---

### 💰 Cost & Coverage Insights

#### Zero Payer Coverage
Identify encounters with no payer coverage and their share of total encounters:

```sql
SELECT
  SUM(CASE WHEN PAYER_COVERAGE = 0 THEN 1 ELSE 0 END) AS zero_payer_coverage,
  COUNT(*) AS total_encounters,
  ROUND(SUM(CASE WHEN PAYER_COVERAGE = 0 THEN 1 ELSE 0 END) / COUNT(*), 2) AS pct_zero_payer_coverage
FROM `maven_db.encounters`;
```

#### Top 10 Procedures by Frequency
List the most common procedures and their average base cost:

```sql
SELECT 
  DESCRIPTION, 
  COUNT(*) AS num_procedures, 
  ROUND(AVG(BASE_COST)) AS base_cost
FROM `maven_db.procedures`
GROUP BY 1
ORDER BY num_procedures DESC
LIMIT 10;
```

#### Average Claim Cost by Payer
Compare average total claim costs across payers:

```sql
SELECT 
  pa.NAME, 
  AVG(en.TOTAL_CLAIM_COST) AS avg_total_claim_cost
FROM `maven_db.encounters` en
LEFT JOIN `maven_db.payers` pa
  ON en.PAYER = pa.Id
GROUP BY 1
ORDER BY avg_total_claim_cost DESC;
```

#### Top 10 Procedures by Cost
Identify the highest-cost procedures and their frequency:

```sql
SELECT 
  DESCRIPTION, 
  ROUND(AVG(BASE_COST)) AS base_cost, 
  COUNT(*) AS num_procedures
FROM `maven_db.procedures`
GROUP BY 1
ORDER BY base_cost DESC
LIMIT 10;
```

---

### ⏱️ Length of Stay (LOS) Analysis

This query calculates **average Length of Stay (LOS)** by **encounter class**, broken down by **patient age group** and **gender**.  
Results show notable patterns — for instance, **males aged 31–40** exhibit significantly longer ambulatory LOS compared to other groups.

*(Dashboard screenshots can be inserted here to visualize findings.)*

---

### 🔁 Readmissions & Follow-up Visits

#### Readmissions Within 30 Days
Use a window function to calculate readmission intervals per patient:

```sql
SELECT 
  COUNT(DISTINCT PATIENT)
FROM (
  SELECT 
    PATIENT,
    DATE(START) AS cur_start,
    DATE(STOP) AS cur_admission,
    DATE(LEAD(START) OVER (PARTITION BY PATIENT ORDER BY START)) AS next_admission,
    DATE_DIFF(
      DATE(LEAD(START) OVER (PARTITION BY PATIENT ORDER BY START)),
      DATE(STOP), 
      DAY
    ) AS interval_admission
  FROM `maven_db.encounters`
) AS re_admission
WHERE interval_admission < 30;
```

#### Most Frequently Readmitted Patients

```sql
SELECT 
  PATIENT,
  COUNT(interval_admission) AS num_readmissions
FROM (
  SELECT 
    PATIENT,
    DATE(START) AS cur_start,
    DATE(STOP) AS cur_admission,
    DATE(LEAD(START) OVER (PARTITION BY PATIENT ORDER BY START)) AS next_admission,
    DATE_DIFF(
      DATE(LEAD(START) OVER (PARTITION BY PATIENT ORDER BY START)),
      DATE(STOP), 
      DAY
    ) AS interval_admission
  FROM `maven_db.encounters`
) AS re_admission
GROUP BY 1
ORDER BY 2 DESC;
```

#### Patient Details Example
View encounter history for a specific patient:

```sql
SELECT *
FROM `maven_db.encounters`
WHERE PATIENT = '1712d26d-822d-1e3a-2267-0a9dba31d7c8';
```

---

## 📈 Key Insights Summary
- Males aged **31–40** have the **highest average LOS**, especially for **ambulatory** encounters.  
- Approximately **49% of encounters** had **zero payer coverage**.  
- The **most common procedures** are relatively low-cost, while a few specialized ones drive the highest average base costs.  
- A subset of patients shows **multiple readmissions within 30 days**, indicating potential for targeted care interventions.  

---

## 🛠️ Tools & Technologies
- **SQL (Google BigQuery)**
- **Power BI**
- **Google Cloud Platform**
- **Data Visualization & Storytelling**

---

## 📎 Author
👤 **Chingchia Yu**  
Senior Financial Analyst | Data Analytics & BI Specialist  
📫 *[LinkedIn]( https://www.linkedin.com/in/ching-chia-yu-cpa-a87b78126/)* | *[GitHub](#)*
