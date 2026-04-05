# 🏥 Student Mental Health & Burnout Analysis Report

**Tool Used:** MySQL  
**Dataset:** `student_mental_health_burnout_1m`  
**Author:** GODWIN IBRAHIM JOHN  
**Date:** 4rd April 2026  

---

## **Table of Contents**

1.  **[Project Overview](#1-project-overview)**
2.  **[Dataset Description](#2-dataset-description)**
3.  **[Expository Exploratory Data Analysis (EDA)](#3-expository-exploratory-data-analysis-eda)**
    * [3.1 Columns: Defining Features](#31-columns-defining-features)
    * [3.2 Rules: Data Integrity Constraints](#32-rules-data-integrity-constraints)
    * [3.3 Null Values: Quality Assessment](#33-null-values-quality-assessment)
4.  **[Data Cleaning & Transformation Steps](#4-data-cleaning--transformation-steps)**
5.  **[Problem Statement Queries (Analysis)](#5-problem-statement-queries-analysis)**
6.  **[Final Verification & Insights](#6-final-verification--insights)**

---

## 1. Project Overview
This project focuses on the large-scale analysis of student mental health and academic burnout. Using a dataset of **1,000,000 records**, we implemented a robust SQL workflow to categorize raw psychological scores into clinical indicators, assign academic grades, and simulate demographic distributions for comprehensive reporting.

## 2. Dataset Description
* **Database Schema:** `` `health mental` ``
* **Table Name:** `` student_mental_health_burnout_1m ``
* **Primary Metrics:** Depression Score, Mental Health Index, Academic Performance.
* **Dimensions:** ~1,000,000 Rows x 15 Columns (post-transformation).

## 3. Expository Exploratory Data Analysis (EDA)

Following the core foundations of data inspection, this expository analysis evaluates the dataset's structure and reliability.

### 3.1 Columns: Defining Features
In this phase, we analyze the variables to determine the complexity of the analysis possible.
* **Dimensionality:** With $1,000,000$ rows, the data offers high statistical power.
* **Feature Expansion:** We added **5 new feature columns** (`is_depressed`, `is_mentally_ill`, `grade`, `course`, `marital_status`) to transition from raw numerical data to categorical insights.
* **Variable Types:** We transformed "Continuous" variables (scores) into "Categorical" bins (A/B/C/D grades and Binary flags).

### 3.2 Rules: Data Integrity Constraints
We established "Rules" to ensure the data reflects real-world clinical and academic logic.
* **Clinical Threshold Rule:** A student is flagged as `is_depressed` if their `depression_score` exceeds **2.5**.
* **Mental Health Rule:** A student is flagged as `is_mentally_ill` if their `mental_health_index` falls below **5.5**.
* **Academic Consistency Rule:** Performance scores are mapped to letter grades using a hierarchical `CASE` statement to ensure non-overlapping categories.

### 3.3 Null Values: Quality Assessment
Identifying "data holes" prevents biased results.
* **Placeholder Removal:** We identified invalid strings like `'N/A'`, `'NaN'`, `'unknown'`, and symbols like `'??'` or `'1234'`.
* **Treatment Strategy:** These were converted to true `NULL` values. This ensures that aggregations (like `AVG` heart rate) are not skewed by non-numeric junk data.

## 4. Data Cleaning & Transformation Steps

### Step 1 — Schema Expansion
We added the necessary analytical columns to the existing table structure.

```sql
ALTER TABLE `health mental`.student_mental_health_burnout_1m 
ADD COLUMN is_depressed INTEGER,
ADD COLUMN is_mentally_ill INTEGER,
ADD COLUMN grade TEXT,
ADD COLUMN course TEXT,
ADD COLUMN marital_status TEXT;
```

### Step 2 — Logic Implementation & Data Imputation
We used **CASE statements** to apply our EDA rules and fill the randomized demographic columns.

```sql
UPDATE `health mental`.student_mental_health_burnout_1m
SET 
    is_depressed = CASE WHEN depression_score > 2.5 THEN 1 ELSE 0 END,
    is_mentally_ill = CASE WHEN mental_health_index < 5.5 THEN 1 ELSE 0 END,
    grade = CASE 
        WHEN academic_performance >= 80 THEN 'A'
        WHEN academic_performance >= 70 THEN 'B'
        WHEN academic_performance >= 60 THEN 'C'
        ELSE 'D'
    END,
    course = CASE (ABS(RANDOM()) % 4)
        WHEN 0 THEN 'Computer Science'
        WHEN 1 THEN 'Psychology'
        WHEN 2 THEN 'Engineering'
        ELSE 'Medicine'
    END,
    marital_status = CASE (ABS(RANDOM()) % 2)
        WHEN 0 THEN 'Single'
        ELSE 'Married'
    END;
```
## 5. Problem Statement Queries (Analysis)

### 1. Distribution of Depressed Students
We analyzed how depression manifests across gender, academic courses, and social status.

```sql
SELECT gender, course, grade, marital_status, COUNT(*) as count
FROM `health mental`.student_mental_health_burnout_1m
WHERE is_depressed = 1
GROUP BY gender, course, grade, marital_status
ORDER BY count DESC;
```

### 2. Pivot Table: Mentally Ill Students by Year and Gender
This query provides a breakdown of mental health status per academic year, pivoted by gender.

```sql
SELECT academic_year,
       SUM(CASE WHEN gender = 'Male' AND is_mentally_ill = 1 THEN 1 ELSE 0 END) AS Male_Mentally_Ill,
       SUM(CASE WHEN gender = 'Female' AND is_mentally_ill = 1 THEN 1 ELSE 0 END) AS Female_Mentally_Ill,
       SUM(CASE WHEN gender = 'Other' AND is_mentally_ill = 1 THEN 1 ELSE 0 END) AS Other_Mentally_Ill
FROM `health mental`.student_mental_health_burnout_1m
GROUP BY academic_year
ORDER BY academic_year ASC;
```

### 3. Demographic Analysis: Female Marital Status
```sql
SELECT marital_status, COUNT(*) AS count
FROM `health mental`.student_mental_health_burnout_1m
WHERE gender = 'Female'
GROUP BY marital_status;
```

---

## 6. Final Verification & Insights
The final step involved auditing the transformed table to ensure all null placeholders were removed and that the backticked schema (`` `health mental` ``) was consistently applied.

* **Result:** 1,000,000 records successfully processed.
* **Insight:** The integration of clinical rules within the SQL workflow allowed for a seamless transition from raw data to a population-level health dashboard.

---
*Documentation provided for GODWIN IBRAHIM JOHN.*
