#  Population Data  Pipeline

This project automates the ingestion, storage, and analysis of public data from the U.S. Bureau of Labor Statistics (BLS) and DataUSA API. It leverages AWS services and PySpark within AWS Glue to deliver a production-ready ETL pipeline.

---

## Part 1: AWS S3 & Sourcing Datasets

**Objective:** Republish the BLS dataset to Amazon S3 and ensure the files remain in sync.

- Source: [https://download.bls.gov/pub/time.series/pr/pr.data.0.Current](https://download.bls.gov/pub/time.series/pr/pr.data.0.Current)
- User-Agent headers are set to comply with BLS access requirements and avoid `403 Forbidden` errors.
- The pipeline checks for new or deleted files from the source and prevents duplicate uploads.
- File uploads are managed dynamically without hardcoded names.



---

##  Part 2: Fetching Population Data via API

**Objective:** Query and persist U.S. population data from the [DataUSA API](https://datausa.io/about/api/).

- API Endpoint: `https://datausa.io/api/data?drilldowns=Nation&measures=Population`
- Data is stored as a timestamped `.json` file in S3 under `population-data/`
- Automates snapshot generation for historical queries and reproducibility

---

##  Part 3: Data Analysis with PySpark in AWS Glue

**Objective:** Perform meaningful analysis on labor and population datasets using distributed processing.

### ➤ A. Mean and Standard Deviation of Population

- Filters API data for the years 2013 to 2018
- Computes:
  - Mean U.S. population
  - Standard deviation of population over selected years

### ➤ B. Best Year for Each `series_id` Based on Total Quarterly Value

- From the BLS dataset:
  - Aggregates quarterly values by year
  - Identifies the year with the highest total per `series_id`

**Example output:**
```
series_id       year    value
PRS30006011     1996    7
PRS30006012     2000    8
```

### ➤ C. Population-Adjusted Report for Target Series

- Filters for:
  - `series_id = PRS30006032`
  - `period = Q01`
- Joins with population data to display matching year

**Sample row:**
```
series_id       year    period    value    population
PRS30006032     2018    Q01       1.9      327167439
```

✔️ Joins structured CSV and JSON datasets with broadcast optimization for performance.

---

## Tech Stack

- **AWS S3**: Object storage for CSV and JSON datasets
- **AWS Glue + PySpark**: Distributed ETL and analytics processing
- **Boto3 & Requests (Python)**: API and S3 integration
- **Structured Data Formats**: CSV, JSON



