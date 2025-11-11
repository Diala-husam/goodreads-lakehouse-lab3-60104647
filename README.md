# DSAI 3202 – Lab 3: Data Preprocessing on Azure Databricks  
**Student:** Diala Hussein  
**ID:** 60104647  
**Course:** DSAI 3202 – Data Engineering  
**Platform:** Azure Databricks  
**Dataset:** Goodreads Reviews Dataset  

---

## Objective  
The purpose of this lab is to perform **data preprocessing on the Goodreads dataset** using Azure Databricks.  
This lab covers the **Silver** and **Gold** stages of the data lake architecture and focuses on data cleaning, transformation, and validation in preparation for advanced analytics and modeling.

---

## Project Structure  

Lab3_Goodreads_Preprocessing
│
├── load_silver_data_60104647.ipynb # Cleans and curates raw JSON data into the Silver layer
├── Goodreads_Cleaning_GoldLayer_60104647.ipynb # Performs final transformations and validation before saving Gold data
└── README.md # Documentation for all implemented steps



---

## Environment Setup  

### **Platform Used**
- Azure Databricks Workspace  
- Cluster: `goodreads-cluster-60104647`  
- Spark Version: 13.x LTS (includes Apache Spark 3.4.1, Scala 2.12)  
- Runtime: Databricks Runtime 13.3 or higher  

### **Datasets**
Data was uploaded to the **Azure Data Lake Storage Gen2** under:  
abfss://lakehouse@goodreadsreviewsgen2.dfs.core.windows.net/



Raw JSON files:
- `book_reviews.json`
- `book_details.json`
- `authors.json`
- `users.json`

---

## 🪄 Workflow Summary  

### **Step 1: Silver Layer Processing (`load_silver_data_60104647.ipynb`)**
1. Loaded the raw JSON files from the **Bronze layer** into Spark DataFrames.  
2. Flattened nested structures and handled schema normalization.  
3. Removed duplicate and null values.  
4. Standardized column names, especially date columns (`date_updated` instead of `date_added`).  
5. Validated transformations by checking:
   - Missing values  
   - Numeric distributions (`rating`, `n_votes`)  
   - Date ranges and invalid entries  
6. Saved the cleaned output into the **Silver Layer** in Parquet format.

---

### **Step 2: Gold Layer Processing (`Goodreads_Cleaning_GoldLayer_60104647.ipynb`)**
1. Loaded the curated Silver DataFrames.  
2. Applied advanced feature engineering and aggregations:
   - Grouped reviews by book and user IDs  
   - Computed review statistics: `avg_rating`, `n_votes`, `review_length_in_words`  
   - Extracted and validated temporal features from `date_updated`  
3. Validated the integrity of numeric and date columns to ensure no anomalies.  
4. Saved the final dataset into the **Gold Layer** under:
abfss://lakehouse@goodreadsreviewsgen2.dfs.core.windows.net/gold/


---

## Data Validation Summary  

| Validation Check | Description | Result |
|------------------|-------------|---------|
| Missing values | Checked for nulls in rating, n_votes, and dates | Clean |
| Invalid ratings | Ensured all ratings were between 0–5 | Clean |
| Negative votes | Verified that n_votes ≥ 0 | Clean |
| Date range | Verified date_updated ≤ current_date() | Valid |
| Review length | Derived word count for each review | Computed |

---

## Outputs  

| Layer | File | Format | Description |
|-------|------|---------|-------------|
| Silver | `silver_reviews.parquet` | Parquet | Cleaned and standardized review data |
| Gold | `gold_reviews.parquet` | Parquet | Final transformed and validated dataset |

---

## Reflections & Key Learnings  

- Gained hands-on experience using **PySpark for ETL pipelines**.  
- Learned to debug Azure storage authentication errors (`401` issues with SAS/Access keys).  
- Improved data quality through validation, deduplication, and schema corrections.  
- Understood the multi-layer structure of data lakes: **Bronze → Silver → Gold**.


