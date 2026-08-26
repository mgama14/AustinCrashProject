## Austin Crash Project: Purpose and Scope

This project was built around the Austin Crash Report dataset, with the goal of creating a full data warehouse pipeline that could actually support real-world decision-making for people like city officials or transportation planners trying to make the roads safer.

I wanted to design something with the ability to answer meaningful, day-to-day questions for that could help improve real world issues!

### Business Objectives

My warehouse was developed to help answer questions like:

- **Where are the most dangerous roads in Austin?**  
  This is useful for deciding where to place signage, redesign roadways, or increase enforcement.

- **What patterns exist in crash severity and cost?**  
  This is valuable insight into how crash types and financial impact shift over time.

- **How have crash trends changed year to year?**  
  This is helpful for tracking progress, budgeting, and public reporting.

### Functional Goals

To support those objectives, I made sure the system could:

- Filter and query crash data by location (using provided lat/long) to identify hotspots  
- Break down crash patterns by vehicle type, highway status, and more  
- Track total crash costs over time, whether by year, month, or custom filters  
- Generate clear dashboards and reports for anyone who needs to visualize the data

### Data Source

I used the [Austin Traffic Crash Report dataset](https://data.austintexas.gov/Transportation-and-Mobility/Austin-Crash-Report-Data-Crash-Level-Records/y2wy-tgr5/about_data), which is publicly available through the City of Austin’s Open Data Portal.

Each row represents an individual crash that happened within Austin, including info like severity level, date, time, roadway type, and context like whether it happened on a highway or private property.

- A full data dictionary is available on the portal.  
- A [local Excel copy](https://github.com/mgama14/AustinCrashProject/blob/main/documents/Data_Dictionary.xlsx) is also included in this repository for easy access.

---

## Information Architecture

The data moves through a structured pipeline, starting from raw extraction and ending in a fully modeled, query-ready warehouse. Hypothetical users like city planners or public safety analysts would be able to run queries and build dashboards to support location-based or time-based decisions.

_See the diagram I created below for a high-level overview of the process._

![Information Architecture](https://github.com/user-attachments/assets/66834e0b-7e2f-47fb-be42-712382abdeab)

- **Source**: Data comes from the Austin Traffic Crash Report dataset.
- **Gather**: Exported as CSV files from the Austin Open Data Portal.
- **Clean**: Dropped irrelevant fields (like temporary flags or deletion indicators) that weren’t useful for the project goals.
- **Reformat**: Renamed columns for clarity and ease of use.
- **Transform**: Created new columns (such as crash cost indicators) to support analysis.
- **Load**: Cleaned and transformed data was loaded into Azure Blob Storage and modeled into a star schema.
- **Warehouse Access**: End users can query and create dashboards from the final warehouse. The data is read-only at this stage.

---

## Data Architecture

This project follows a bottom-up Kimball-style design. The focus was on building a single, scalable data mart structured around crash incidents. I wanted the data to remain organized, flexible, and easy to extend in the future if additional datasets were added.

![Data Architecture](https://github.com/user-attachments/assets/d5eabf4d-c998-4b89-a790-1a6e25356227)

- **Data Source**: Downloaded from the [Austin Open Data Portal](https://data.austintexas.gov/).
- **Data Storage**: CSV files stored in Azure Blob Storage as a staging layer.
- **Cleaning & Transformation**: Standardized columns, dropped unnecessary fields, and handled nulls using Python and SQL.
- **Data Mart**: Modeled into a star schema with one fact table and four dimension tables using DbSchema.
- **Data Warehouse**: Final warehouse is hosted in Snowflake and supports analysis and reporting.

---

## Technical Architecture

The technical side of this project follows a basic ETL flow using Python, cloud storage, and Snowflake. Once the cleaned data is ready, it feeds directly into Power BI for visualization.

![Technical Architecture](https://github.com/user-attachments/assets/bbb0281d-7a1c-44e9-ac62-fcd52a01edba)

- Data was extracted using a custom Python script.
- Raw crash records were uploaded directly to Azure Blob Storage.
- A Python-based ETL process handled cleaning, transformation, and schema modeling.
- Final output was loaded into a Snowflake data warehouse.
- Power BI was used to build an interactive dashboard based on the modeled data.

---

## Medallion Architecture: Bronze, Silver, Gold Layers

This project uses a Medallion Architecture approach to keep each stage of the data lifecycle clean and traceable:

- **Bronze (Raw)**: Raw Austin crash data stored as-is in Azure Blob Storage.
- **Silver (Cleaned)**: Data was cleaned using Python in `etl_crashes.ipynb`, where nulls were handled, columns were renamed, and unnecessary fields were removed.
- **Gold (Star Schema)**: Final version modeled into a dimensional star schema with one fact table and four supporting dimensions.

---

## Dimension Modeling

To make the crash data easier to query and analyze, I modeled it into a classic star schema using DbSchema. This design allows the user to slice and filter the data across key categories.

![Star Schema](https://github.com/user-attachments/assets/cd0948e7-06f9-4cd1-ad54-aebb57337f4a)

**Fact Table:**
- `Fact_Crashes`: Contains crash counts, cost estimates, and key event metrics.

**Dimension Tables:**
- `Dim_TxDot`: Indicates whether the crash occurred on a TxDOT highway, private driveway, or construction site.
- `Dim_Severity`: Crash severity, coded from 0 to 5.
- `Dim_Location`: Latitude, longitude, and road location details.
- `Dim_Calendar`: Date, time, and whether the crash occurred on a weekend or holiday.

---

## Power BI Dashboard

To showcase insights from the warehouse, I created a Power BI dashboard. It allows users to filter crashes by year and severity, explore time-based patterns, and identify problem areas across Austin.

![Dashboard Visualizations](https://github.com/user-attachments/assets/75717f89-5030-4b2c-bad6-38b6e04d7207)

The dashboard includes:
- A map of crash severity by location (latitude and longitude)
- Slicers for filtering by crash year and severity
- A pie chart showing crash severity distribution
- A column chart of crashes per year
- A line chart for total crash costs over time
- A heat map of crash counts by weekday and time of day

---

## Key Insights

A few patterns stood out during the analysis:

- **Fatal crashes made up a large portion of total incidents**; I found this to be pretty surprising, and I think it highlights a major public safety issue in Austin.
- **Total crash costs seem generally pretty volatile, but predictably, there was a giant drop in costs around 2020**; this makes perfect sense, seeing as how in general, there were a lot less people driving during the pandemic quarantine, likely leading to less crashes and reduced personal crash costs overall.
- **Weekday crashes at night had the highest cost impact**, I concluded this was due to exhausted drivers commuting home from work. It makes sense that this demographic would have a large amount of crashes; they might be too fatigued to be as diligent as they need to be on the road.
---

## Final Thoughts

Overall, the project was a success. I was able to build a working data pipeline from raw data, create a warehouse from it, and import it into Power Bi to create a powerful and informative dashboard. All visualizations are based on the real-world crash data and modeled for potential decision making for city officials or urban planners. I think that this project is very powerful, and can be used effectively to help navigate resource allocation for Austin.

If I continue this project, I’d love to:
- Add weather data to provide more context
- Filter by vehicle type to study who’s most at risk
- Expand the schema to include external datasets and multi-year comparisons
