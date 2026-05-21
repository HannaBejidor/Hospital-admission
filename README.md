# Hospital-admission SQL Project

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



### Understanding the nature of the data (data structure of hospital_admissions table):
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

