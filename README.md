Lab Overview

This lab focused on processing, cleaning, and transforming Goodreads review data through Microsoft Fabric and Databricks to produce a curated Gold dataset ready for analytics and modeling.

Steps Completed
Data Ingestion and Preparation

Imported the raw Goodreads reviews dataset into Microsoft Fabric.

Transformed and cleaned text columns (capitalization, removal of invalid characters, etc.).

Encountered long evaluation delays in Fabric (“evaluation was canceled”), so the final preprocessing was continued in Databricks.

Cleaning the Data in Databricks
Create a new notebook

Created a new Python notebook in Databricks.

Connected to the Fabric dataset located in the Silver layer (curated_reviews).

Load curated dataset
df = spark.read.format("delta").load("abfss://lakehouse@goodreadsreviewsgen2.dfs.core.windows.net/gold/curated_reviews")

Clean the data

Removed unused columns (e.g., author_id, review_text where null).

Dropped duplicate reviews by review_id and (user_id, book_id) combinations.

Trimmed extra spaces, standardized text case, and removed malformed rows.

Dropped reviews with extremely short text.

Converted data types (IDs as integers, dates as timestamps, etc.).

Feature preparation

Computed two main features for each book:

avg_rating_per_book — Average rating per book.

num_reviews — Number of reviews per book.

Aggregation logic:

agg_df = df.groupBy("book_id").agg(
    F.avg("rating").alias("avg_rating_per_book"),
    F.count("*").alias("num_reviews")
)

Save results to Gold layer

Tried saving to:

abfss://lakehouse@goodreadsreviewsgen2.dfs.core.windows.net/gold/features_v1


Encountered DELTA_CANNOT_CREATE_LOG_PATH (no write permissions).

Workaround: Saved to Databricks internal storage:

(agg_df.write
    .format("delta")
    .mode("overwrite")
    .option("overwriteSchema", "true")
    .save("/mnt/tmp/features_v1")
)

Verification

Reloaded dataset:

display(spark.read.format("delta").load("/mnt/tmp/features_v1"))


Output verified successfully (863 rows, correct schema).

Final Result
Column	Description
book_id	Unique identifier for each book
avg_rating_per_book	Average rating across reviews
num_reviews	Number of reviews per book

Successfully cleaned, aggregated, and stored the Gold dataset in Delta format within Databricks.

Notes

Fabric evaluation was canceled repeatedly, leading to migration of processing to Databricks.

Delta format ensures reliable performance for large-scale queries and modeling.

Future improvement: Reconnect ADLS permissions to enable direct Gold layer writing.
