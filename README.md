# Hospital-admission Project in SQL
## Project summary:
This project was made to understand and tackle existing problems in the hospital, such as:
- Operational Problem
- Financial or resource problem
- Public health problem
- Performance and quality problem
## Aim of this project is to answer core business problems
1. Admission trend overview
2. Length of Stay (LOS) Analysis: average stay, diagnoses with longest stay, does age/gender have affect on LOS?
3. Diagnosis & Disease Burden: top diagnosis, icd10 chapter dominate admission?
4. Demographic analysis: age and gender distribution, age group with most admission, diagnosis affect elderly patients
5. Provider Performance: provider with highest admission, provider with longest LOS, are some provider overload? 
6. Repeat admission / patient utilization: which patients are repeatedly admitted, diagnoses lean to readmissions, chronic conditions causing repeat visits?


#### Dataset from this project was sourced from two tables;

📄 hospital_admissions.csv
📄 icd10_codes.csv


### Create 2 tables under a new schema, hc_analysis before filling it with data from an excel spreadsheets
```
CREATE SCHEMA hc_analysis;
```
#### Table 1: hospital_admission
```
CREATE TABLE hospital_admissions (
Admission_id INT PRIMARY KEY
,patient_id VARCHAR (20)
,admission_date DATE
,discharge_date DATE
,primary_diagnosis_code VARCHAR (20)
,procedure_code VARCHAR (20)
,age INT
,gender CHAR (1)
,provider_code VARCHAR (20)
,ccg_code VARCHAR (20)
);
```
#### Table 2: icd10_codes
```
CREATE TABLE icd10_codes (
code VARCHAR (20) PRIMARY KEY
,description TEXT
,chapter_name TEXT
);
```



### Data structure of hospital_admissions table:
| Column name | Description | 
|---|---|
|Admission_id| Primary key, unique identifier (patient id is more relevant) |
|patient_id| Unique identification of patients |
|admission_date| Date of admission in the hospital |
| discharge_date| Date of patients discharge from the hospital |
|primary_diagnosis_code| ICD10 diagnostic code |
|procedure_code| National clinical coding standards OPCS-4 procedure codes |
|age| Age at admission |
|gender| Gender of patient |
|provider_code| NHS Trust/hospital code |
|ccg_code| Clinical Commissioning Group (now replaced by Integrated care boards (ICBs) from 2022 |

### Data structure of ICD10 diagnostic codes table:
| Column name | Description | 
|---|---|
|Code| Unique identifyer for disease|
|description| name of the disease/condition|
|chapter name| Body system/specific conditions/external causes|

## Understand the nature of the data

``` SELECT * FROM hospital_admissions LIMIT 50; ```

``` SELECT * FROM icd10_codes; ```


1. Find out the number on admission monthly/yearly. Does the admission increases over time? Was there a seasonal spike? Which diagnosis with the most admission?
```
-- 1. Admission trend per month --
SELECT 
	date_format(admission_date, '%Y-%m') AS admission_month,
	COUNT(admission_id) AS admission_over_time
FROM hospital_admissions
GROUP BY admission_month
ORDER BY admission_month ASC;
```
#### Insight:

2.  Length of stay (LOS) Analysis: What is longest stay in the hospital? which diagnosis leads to a longest stay? Which provider has higher LOS?
```
-- average length of stay-- 
SELECT patient_id
	,ROUND(avg(datediff(discharge_date, admission_date)),1) AS ave_los
FROM hospital_admissions
GROUP BY patient_id
ORDER BY ave_los desc;
```
#### Insight:
```
-- top 5 diagnosis based on LOS --
SELECT
  h.patient_id,
  h.primary_diagnosis_code as primary_code,
  ic.description,
  h.discharge_date,
  h.admission_date,
  (datediff(h.discharge_date, h.admission_date)) AS los
FROM hospital_admissions as h
  JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
ORDER BY los DESC
LIMIT 5;
```
#### Insight:
```
-- diagnoses with longest stay --
SELECT
  h.primary_diagnosis_code as primary_code,
  ic.description,
  ROUND(avg(datediff(h.discharge_date, h.admission_date)),1) AS ave_los
FROM hospital_admissions as h
  JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
GROUP BY primary_code
ORDER BY ave_los DESC; 
```
#### Insight:

3. Diagnosis & Disease Burden: What are the top diagnosis? Which ICD10 code chapter dominates the admission?
```
-- top 5 diagnosis --
SELECT
  h.primary_diagnosis_code AS code,
  ic.description,
  COUNT(h.admission_id) AS top_diagnosis
FROM hospital_admissions as h
  JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
GROUP BY code
ORDER BY top_diagnosis DESC
LIMIT 5; 
```
#### Insight:

4. Demographic analysis: Observe Age and gender distribution. Which age group with most admission? Which diagnosis is more prevalent in elderly people (aged ≥65)

```
-- Age aggregation --
SELECT 
	MIN(age) AS minimun_age,
	MAX(AGE) AS maximum_age,
  AVG(age) AS average
FROM hospital_admissions;
```
#### Insight:
```
-- age group distribution --
SELECT age_group, count(*) AS admission_count
FROM
	(
SELECT age,
   CASE
		WHEN age BETWEEN 0 AND 10 THEN '0-10'
		WHEN age BETWEEN 11 AND 20 THEN '11-20'
		WHEN age BETWEEN 21 AND 30 THEN '21-30'
		WHEN age BETWEEN 31 AND 40 THEN '31-40'
		WHEN age BETWEEN 41 AND 50 THEN '41-50'
        WHEN age BETWEEN 51 AND 60 THEN '51-60'
        WHEN age BETWEEN 61 AND 70 THEN '61-70'
        WHEN age BETWEEN 71 AND 80 THEN '71-80'
ELSE '80+'
END AS age_group
FROM hospital_admissions
	) as grouped
    GROUP BY age_group
    ORDER BY age_group ASC;
```
#### Insight:   
```            
-- age group with most admission --
SELECT age_group, count(*) AS admission_count
FROM
	(
SELECT patient_id,admission_date,age,
CASE
		WHEN age BETWEEN 0 AND 10 THEN '0-10'
		WHEN age BETWEEN 11 AND 20 THEN '11-20'
		WHEN age BETWEEN 21 AND 30 THEN '21-30'
		WHEN age BETWEEN 31 AND 40 THEN '31-40'
		WHEN age BETWEEN 41 AND 50 THEN '41-50'
        WHEN age BETWEEN 51 AND 60 THEN '51-60'
        WHEN age BETWEEN 61 AND 70 THEN '61-70'
        WHEN age BETWEEN 71 AND 80 THEN '71-80'
ELSE '80+'
END AS age_group
	FROM hospital_admissions
	) as grouped
GROUP BY age_group
ORDER BY admission_count DESC;
```
#### Insight:

```
-- gender distribution --
SELECT  gender,
        COUNT(gender) AS count
FROM hospital_admissions
GROUP BY gender;
```
#### Insight:
```
-- diagnosis affect on elderly/ top diagnosis patients aged ≥ 65--
SELECT
  h.age,
  h.primary_diagnosis_code AS code,
  ic.description,
  ic.chapter_name,
  COUNT(h.admission_id) AS top_diagnosis
FROM hospital_admissions as h
  JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
WHERE age >=65
GROUP BY code, age
ORDER BY top_diagnosis DESC;
```
#### Insight:
