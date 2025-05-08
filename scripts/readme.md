# Austin Traffic Crashes ETL Pipeline

These scripts perform end-to-end extraction, transformation, and loading (ETL) of traffic crash data from the City of Austin Open Data Portal. My pipeline stages raw crash data in Azure Blob Storage and then processes and loads it into a Snowflake data warehouse using a star schema model.

## Files Overview

### 1. extract_crashes.ipynb – Extraction & Upload

Web scrapes crash records from Austin Open Data Portal.

Stores the data as a CSV in Azure Blob Storage under a container.

Reads Azure credentials from config.json.

### 2. etl_crashes.ipynb – ETL Process

Reads the cleaned data from Azure.

Performs data cleaning, column renaming, filtering, and null handling.

Constructs a dimensional model:

DIM_CALENDAR

DIM_LOCATION

DIM_TXDOT

DIM_SEVERITY

FACT_CRASHES

Uses SQLAlchemy to upload all tables into Snowflake in chunks for reliability.

Creates necessary surrogate keys and foreign key references.

## ⚙️ Configuration – config.json

Store your credentials in a config.json file in this format:

{
  "AZURE_CONNECTION_STRING": "<your_azure_connection_string>",
  "austin_container": "austincrashes_raw",
  "SNOWFLAKE_USER": "<your_user>",
  "SNOWFLAKE_PASSWORD": "<your_password>",
  "SNOWFLAKE_ACCOUNT": "<your_account>",
  "SNOWFLAKE_DATABASE": "<your_db>",
  "SNOWFLAKE_SCHEMA": "<your_schema>",
  "SNOWFLAKE_WAREHOUSE": "<your_warehouse>"
}

This file is used to connect to both Azure and Snowflake across the two notebooks.

## 🚀 How to Run

### 1. Extraction Phase:

Open extract_crashes.ipynb.

Run all cells to extract, clean, and upload the raw crash data to Azure.

### 2. ETL Phase

Open etl_crashes.ipynb.

Run all cells to:

Load data from Azure.

Process and clean it.

Create dimension and fact tables.

Upload tables to Snowflake using chunked batch inserts.

## ✅ Output

At the end of the process, your Snowflake warehouse will contain:

One raw table (CRASHES_RAW)

Four dimension tables

One fact table

These can now be used for Power BI dashboards and analytics.
