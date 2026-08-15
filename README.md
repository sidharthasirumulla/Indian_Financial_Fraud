# Indian Financial Fraud Analytics: End-to-End Azure Data Engineering Pipeline

## Project Overview

This project builds an end-to-end data pipeline on Microsoft Azure to process, transform, analyze, and visualize Indian financial transaction data for fraud detection. The platform ingests financial datasets from external sources into Azure cloud storage, cleans and transforms large-scale transaction data via Apache Spark, exposes optimized models through cloud data warehousing, and displays interactive dashboards for real-time monitoring.

---

## Architecture Pipeline
'''

[ Kaggle Data Source ] 
       │
       ▼
[ Azure Storage / Container ]
       │
       ▼
[ Azure Data Factory (Data Ingestion) ]
       │
       ▼
[ Raw Storage (ADLS Gen2 Container) ]
       │
       ▼
[ Azure Databricks (PySpark Transformations) ]
       │
       ▼
[ Transformed Data (ADLS Gen2 Container) ]
       │
       ▼
[ Azure Synapse Analytics (Data Warehouse / Lake Database) ]
       │
       ▼
[ Power BI (Interactive Dashboards) ]
'''[cute:1]

---

## Key Features & Azure Services Used

* **Data Source**: Kaggle Financial Fraud Datasets containing raw transactional, customer, card, and merchant data[cite: 1].
* **Azure Storage Accounts & ADLS Gen2**: `financialstorageact` with containers (`financial-container`) partitioned into raw and transformed data directories[cite: 1].
* **Azure Data Factory (ADF)**: `financial-fraud-adf` Orchestrates pipelines (`Data-Ingestion`) executing parallel batch data ingestion tasks[cite: 1].
* **Azure Databricks**: Spark-based data cleaning, schema enforcement, aggregation, mounting storage via Service Principal credentials, and writing curated datasets back to storage[cite: 1].
* **Azure Synapse Analytics**: Data warehouse layer (`financialfraud-saw`) hosting `FinancialDB` SQL models and executing analytics queries on fraud counts and amounts[cite: 1].
* **Power BI Desktop & Service**: Interactive analytical dashboards featuring key performance metrics, risk breakdown, device/state fraud distribution, and temporal trend analyses[cite: 1].

---

## Data Architecture & Model Schema

The data model is organized into key analytical tables centered around transactional fraud metrics[cite: 1]:

| Table Name | Description | Key Attributes |
| :--- | :--- | :--- |
| **`transactions`** | Core Fact Table | `Transaction_ID`, `Customer_ID`, `Merchant_ID`, `Card_ID`, `Transaction_Amount`, `Fraud_Flag`, `Transaction_Date`, `Device_Type`[cite: 1] |
| **`customer`** | Dimension Table | `Customer_ID`, `Customer_Name`, `Age`, `Gender`, `State`, `City`, `Annual_Income`, `Customer_Risk_Category`, `Customer_Risk_Score`[cite: 1] |
| **`merchant`** | Dimension Table | `Merchant_ID`, `Merchant_Name`, `Merchant_State`, `Merchant_Category`[cite: 1] |
| **`card`** | Dimension Table | `Card_ID`, `Card_Type`, `Card_Category`, `Expiration_Date`[cite: 1] |
| **`Date Table`** | Dimension Table | `Date`, `Year`, `Month`, `Quarter`[cite: 1] |


---

## Setup & Implementation Guide

### 1. Azure Storage & Service Principal Setup
* Create Azure Resource Group `Resource-Financial`[cite: 1].
* Provision Azure Data Lake Storage Gen2 account `financialstorageact` and container `financial-container`[cite: 1].
* Register an App (Service Principal) in Microsoft Entra ID with `Storage Blob Data Contributor` roles to enable Databricks storage mounting[cite: 1].

### 2. Pipeline Ingestion (Azure Data Factory)
* Create ADF resource `financial-fraud-adf`[cite: 1].
* Build pipeline `Data-Ingestion` containing Copy Data activities to load data into the `raw-data` path[cite: 1].

### 3. Transformation (Azure Databricks)
* Configure Databricks workspace `financial-fraud-data-db`[cite: 1].
* Run notebook script `financial_fraud_data_analysis_transformation` to mount ADLS Gen2, clean data, compute aggregate metrics, and export curated datasets to `transform-data`[cite: 1].

python
# Sample Databricks Storage Mount
configs = {
    "fs.azure.account.auth.type": "OAuth",
    "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
    "fs.azure.account.oauth2.client.id": "<CLIENT_ID>",
    "fs.azure.account.oauth2.client.secret": "<CLIENT_SECRET>",
    "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<TENANT_ID>/oauth2/token"
}

dbutils.fs.mount(
    source = "abfss://financial-container@financialstorageact.dfs.core.windows.net/",
    mount_point = "/mnt/financial-fraud",
    extra_configs = configs
)

### 4. Data Warehousing (Azure Synapse Analytics)
* Set up Synapse workspace `financialfraud-saw` and database `FinancialDB`[cite: 1].
* Query transactions and compute high-risk merchant metrics[cite: 1]:

sql
SELECT 
    Merchant_ID,
    COUNT(*) AS Fraud_Count,
    SUM(Transaction_Amount) AS Fraud_Amount
FROM transactions
WHERE Fraud_Flag = 1
GROUP BY Merchant_ID;


### 5. Data Visualization (Power BI)
* Connect Power BI to Azure Synapse / ADLS Gen2[cite: 1].
* Import star-schema relationships between `transactions` (Fact) and Dimension tables[cite: 1].
* Build visual dashboards tracking Total Fraud Amount, Fraud vs Non-Fraud Cases, Transaction Volume by Risk Level, and State-wise Fraud Cases[cite: 1].

'''
