# goodreads-lakehouse-lab3-60104647

# Goodreads Data Cleaning & Aggregation – Fabric Lab

### Student Information
**Name:** Diala Hussein  
**Course:** Data Engineering / Azure Fabric Lab  
**Dataset:** Goodreads Reviews  
**Goal:** To clean, transform, and aggregate Goodreads review data in **Microsoft Fabric** using **Power Query**, following the provided lab instructions.

---

## Part 1 – Load the Delta Table

- Created a new **Dataflow (Gen2)** in Microsoft Fabric.  
- Used a **Blank Query** and pasted the Power Query M code to connect to my Delta folder:
  ```m
  Source = AzureStorage.DataLake("https://goodreadsreviewsgen2.dfs.core.windows.net/lakehouse/gold/curated_reviews/")

Initially got a few DataFormatError messages (“no valid Delta table at this location”), then filtered only .parquet files using:

Table.SelectRows(Source, each Text.EndsWith([Name], ".parquet"))


Successfully combined the Parquet files — the table preview showed columns like:
review_id, book_id, title, user_id, rating, review_text, n_votes, and date_added.

 Worked: Connection to Data Lake and file combination
Issue fixed: Initially tried connecting to _delta_log — corrected to point at curated_reviews folder.


Part 2 – Clean the Data
a. Adjust Data Types

Used Transform → Data Type to set:

review_id, book_id, user_id → Text

rating, n_votes → Whole Number

date_added → Date

Worked: All type conversions applied
Note: date_added column contained only null values.

b. Handle Missing or Invalid Values

Removed blank rows for rating using:
Transform → Remove Rows → Remove Blank Rows

Verified that rows with valid ratings remained.

Worked: Blank ratings removed

c. Create Derived Column: review_length

Added a new column using:

Text.Length([review_text])


This column measures how long each review is.

Worked: Column created successfully

d. Filter Short Reviews

Kept only reviews with text length > 10 characters using:
Filter Rows → review_length > 10

Worked: Short/empty reviews removed

e. Handle Missing Values

language column was missing entirely from dataset → Skipped this step safely.

n_votes column had nulls, but handled later during transformations if needed.

author_id and name columns were all null → kept as-is (irrelevant for final aggregation).

Decision: Safe to skip missing columns not present in data

f. Trim & Standardize Text

Used:

Transform → Format → Trim

Transform → Format → Capitalize Each Word
on title and review_text columns.

Worked: Clean text formatting applied

Part 3 – Aggregations

Built derived features using Group By:

Average Rating per Book

Grouped by book_id

Operation: Average of rating

New column: avg_rating_per_book

Number of Reviews per Book

Grouped by book_id

Operation: Count Rows

New column: review_count

Issue: When trying to evaluate aggregations, Power Query ran for 10–15 minutes and then stopped with
“Evaluation was cancelled.”
This may be caused by slow Fabric environment or table size.


Part 4 – Publish & Finalization

Attempted to click Publish, but due to evaluation timeout, process couldn’t complete.

Saved all transformations and added this report to GitHub for review.

Progress: Data cleaning steps 100% completed
Pending: Final aggregation publishing (due to Fabric runtime cancellation)
