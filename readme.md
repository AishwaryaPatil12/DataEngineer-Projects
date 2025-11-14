##Sales Data Analytics Pipeline (Databricks + Delta Lake + Power BI)

A complete end-to-end data engineering project built using Databricks, PySpark, Delta Lake, and Power BI, following the Medallion Architecture (Bronze → Silver → Gold).

##Project Workflow
CSV File → Databricks Volume → Bronze Table → Silver Table → Gold Tables → Power BI Dashboard

🛠 Tech Stack

Databricks

PySpark

Delta Lake

DBFS / Volumes

GitHub Integration

Power BI Desktop

##Bronze Layer – Raw Ingestion

Uploaded sales_data.csv to Volumes

Loaded CSV into DataFrame with schema inference

Saved as Delta Bronze Table (no cleaning)
`
df_bronze = spark.read.csv("/Volumes/lakehouse/bronze/rawfiles/sales_data.csv",
                           header=True, inferSchema=True)

df_bronze.write.format("delta").saveAsTable("lakehouse.bronze.sales_bronze")`

##Silver Layer – Cleaning & Transformations

Converted order_date to DATE

Added total_amount = quantity * price

Removed duplicates

`df_silver = (df_bronze
             .withColumn("order_date", F.to_date("order_date", "M/d/yyyy"))
             .withColumn("total_amount", F.col("quantity") * F.col("price"))
             .dropDuplicates())

df_silver.write.format("delta").saveAsTable("lakehouse.silver.sales_silver")`

##Gold Layer – Aggregations
`Daily Sales
df_daily_sales = (df_silver.groupBy("order_date")
                  .agg(F.sum("total_amount").alias("daily_sales")))

Sales by Region
df_sales_region = (df_silver.groupBy("region")
                   .agg(F.sum("total_amount").alias("total_sales"),
                        F.count("*").alias("num_orders")))`

##Power BI Dashboard

Connected Power BI → Databricks SQL Warehouse → imported Gold tables.
![Dashboard]("/Workspace/Users/patilaishwarya422@gmail.com/DataEngineer-Projects/image/SalesDashboard.png")
