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

## 1. Admission Trend overview:
### -- Admission:Patient count ratio --

```
SELECT	COUNT(admission_id) AS admission_count,
		COUNT(DISTINCT patient_id) AS patient_count
FROM hospital_admissions;
```

#### Answer: Total of 500 admissions, with 100 unique patients
<img width="235" height="53" alt="Screenshot 2026-05-24 235416" src="https://github.com/user-attachments/assets/0dc3d529-0b5f-4a9c-9019-dfd08d0cb520" />

### -- 1. Admission trend per month --
```
SELECT 
	date_format(admission_date, '%Y-%m') AS admission_month,
	COUNT(admission_id) AS admission_over_time
FROM hospital_admissions
GROUP BY admission_month
ORDER BY admission_month ASC;
```
#### Answer:
<img width="274" height="304" alt="Screenshot 2026-05-25 000813" src="https://github.com/user-attachments/assets/1cab9265-8569-4210-a837-0808c11763e6" />


## 2. Length of stay (LOS) Analysis:
### -- average length of stay--
```
SELECT
	ROUND(avg(datediff(discharge_date, admission_date)),1) AS ave_los
FROM hospital_admissions;
```
#### Answer: average LOS of = 5.68

### -- top 5 diagnostic chapter based on average los --
```
SELECT 	h.primary_diagnosis_code as primary_code,
		ic.chapter_name,
		ROUND(avg(datediff(h.discharge_date, h.admission_date)),2) AS ave_los
FROM hospital_admissions as h
	JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
GROUP BY primary_code
ORDER BY ave_los DESC
LIMIT 5;
```
#### Answer:
<img width="294" height="133" alt="Screenshot 2026-05-25 002229" src="https://github.com/user-attachments/assets/74bb919e-1db1-44fb-b813-72702c4ced35" />

### -- top diagnoses based on overall average lenght of stay --
```
SELECT
  h.primary_diagnosis_code as primary_code,
  ic.description,
  ROUND(avg(datediff(h.discharge_date, h.admission_date)),1) AS ave_los
FROM hospital_admissions as h
  JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
GROUP BY primary_code
ORDER BY ave_los DESC; 
```
#### Answer:
<img width="400" height="240" alt="Screenshot 2026-05-25 002655" src="https://github.com/user-attachments/assets/0a85c507-504a-41c7-b500-46d9a6e1c5fd" />

## 3. Diagnosis & Disease Burden:
### -- top diagnosis --
```
SELECT
  h.primary_diagnosis_code AS code,
  ic.description,
  COUNT(h.admission_id) AS top_diagnosis
FROM hospital_admissions as h
  JOIN icd10_codes as ic ON h.primary_diagnosis_code = ic.code
GROUP BY code
ORDER BY top_diagnosis DESC; 
```
#### Answer: Most admission:(I10) Essential hypertension of n= 61 ; Least: (N18) Chronic Kidney disease of 38 admission
<img width="599" height="236" alt="Screenshot 2026-05-25 003355" src="https://github.com/user-attachments/assets/c7e060db-0a32-405d-a8f1-ff04864b3e4e" />

## 4. Demographic analysis:
### -- Age aggregation --
```
SELECT 
	MIN(age) AS minimun_age,
	MAX(AGE) AS maximum_age,
  AVG(age) AS average
FROM hospital_admissions;
```
#### Answer: Minimum age = 0 ; Maximun age = 90 years old; Mean age = 44.9 years old

### -- age group distribution --
```
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
#### Answer: 
<img width="208" height="219" alt="Screenshot 2026-05-25 003636" src="https://github.com/user-attachments/assets/8c8300c1-a13c-4f7c-9d0f-d7748c442198" />

### -- age group with most admission --
```            
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
#### Answer: Age group 11-20 years has the highest admission of n= 62; While Age group with lowest admission were 71-80 of n= 45

### -- gender distribution --
```
SELECT	gender,
		COUNT(gender),
		ROUND((COUNT(gender)/ (SELECT COUNT(gender) FROM hospital_admissions)*100),0) AS percentage
FROM hospital_admissions
GROUP BY gender;
```
#### Answer: 49% Male patients (n=245) & 51% Female patients (n=255)

## 5. Provider Performance:
### -- Provider with highest admission--

```
SELECT 	DISTINCT provider_code,
		COUNT(admission_id) AS admission_count
FROM hospital_admissions
GROUP BY provider_code
ORDER BY admission_count DESC;
```
#### Answer:
<img width="262" height="95" alt="Screenshot 2026-05-24 231054" src="https://github.com/user-attachments/assets/82d3aad6-90bc-4f40-8066-aa009bad97c3" />

### -- LOS of each provider --
```
SELECT 	provider_code,
		ROUND(AVG(datediff(discharge_date,admission_date)),2) AS LOS
FROM hospital_admissions
GROUP BY provider_code;
```
#### Answer:
<img width="159" height="93" alt="Screenshot 2026-05-27 180700" src="https://github.com/user-attachments/assets/b465048c-3675-4285-966d-7fdeb1f9a02d" />
