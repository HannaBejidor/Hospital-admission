# Hospital-admission

📄 hospital_admissions.csv [link]()
📄 icd10_codes.csv

### The data has 10 columns, with the following variables
- Admission_id
- patient_id
- admission_date
- discharge_date
- primary_diagnosis_code
- procedure_code
- age
- gender
- provider_code
- ccg_code

### For this project, I will be using 2 tables containing the main data of hospital admissions, the second table is ICD10 codes table with only 3 columns. E.g of what the table is about; A01/Typhoid fever/Infectious diseases

- code
- description 
- chapter_name

Import the 2 tables and load the them to check if the data was imported successfully, using SQL syntax, SELECT * From schema.table_name.

Understanding the nature of the data (data structure):
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

