# Austin Crash Project

### **Business requirements:**

With the Austin Crash Database, my data warehouse project will attempt to fulfill three specific major business requirements:

•	**First**, the proposed warehouse will be used to help analyze which areas in Austin have the most crashes. This can potentially help city officials decide where signage might need improvement, help decide what roads need design updates, and where there might need to be more law enforcement placed to help keep roads safe.

•	**Second**, the warehouse will be used to analyze patterns in crash severity and cost. Looking at things like which years had the most expensive crashes or which types of crashes are more common can help people understand what contributes to crashes and how to prevent them.

•	**Finally**, the warehouse will be able to give users a simple way to see how serious crashes are overall, how they’ve changed over time, and how much they’ve cost the city overall. This could potentially help with things like planning budgets, setting safety goals, or making community reports.

### **Functional requirements:**

•	To assist with finding the crash hotspots, the data warehouse will allow users to query crash data by location, so they can see roads with the highest crash frequency using the latitude and longitude location fields.

•	To be able to filter and aggregate the data based on conditions such as vehicle type TxDOT highway usage to help fulfill the second business requirement of finding what influences crashes.

•	To provide estimated crash costs over time and help users understand the financial impact of crashes over time and how it varies by year, or month. 

•	To be able to allow users to generate dashboards, create visualizations, and export reports on the filtered crash data, supporting visualization of trends over time and by location.

### **Data requirements:**

To achieve the business requirements for this project, I am using the [Austin Traffic Crash Report dataset](https://data.austintexas.gov/Transportation-and-Mobility/Austin-Crash-Report-Data-Crash-Level-Records/y2wy-tgr5/about_data), sourced from the Austin Open Data Portal.
Each row in this dataset is an individual crash accident that occurred in Austin, Texas. The data dictionary is also included in the linked source, and a [copy CSV](https://github.com/mgama14/AustinCrashProject/blob/main/Data_Dictionary.xlsx) is available in the repository.

### **Information Architecture:**

**Overview:** For this project, the information architecture will begin with the data gathered from the Austin Crash Report dataset and will move through the logical stages before being stored in the final data warehouse. Hypothetical users like city officials trying to determine optimal areas for road improvements will be able interact with the data warehouse via query and create visualizations to help with any location/time-based analysis. The flow of this is outlined in the diagram below.

 ![informationarch](https://github.com/user-attachments/assets/66834e0b-7e2f-47fb-be42-712382abdeab)

**Source:** The raw data is all sourced from the Austin Traffic Crash Report data set to begin with.

**Gather:** The data then gathered from Austin’s portal in the form of a csv export. 

**Clean:** The next step would be to remove data not entirely necessary for the business requirements and to remove those irrelevant fields (an example being the “is deleted/is temporary record” columns).

**Reformat:** This will be where fields will be renamed for ease of use.

**Transform:** Where new fields and columns will most likely be added, like columns that help calculate crash severities.

**Load:** The clean and transformed data will then be loaded into Azure blob storage and structured into the star schema.

**Data Warehouse:** Where the prepared data is available for the end users to have read-only access to dashboards and reports to help with their needs. The hypothetical end users will not be able to directly modify any of the data after it is stored in the warehouse. 

### **Data Architecture:**
Overview: I will explain the data architecture for this project to illustrate how the data is collected and processed using the planned tools. The goal is to ensure my data remains organized and accessible at all stages of the project. At the current moment, there are no plans to use multiple datasets. The architecture will follow a bottom-up Kimball style flow, diagrammed below:

 ![data architecture](https://github.com/user-attachments/assets/d5eabf4d-c998-4b89-a790-1a6e25356227)

**Data Source:** As mentioned, the data comes from the Austin Open Data Portal and is downloaded as a CSV. 

**Data Storage:** Once the CSV is exported and downloaded, it will be stored in Microsoft Azure blob storage as the staging location. The raw data will be preserved here.

**Cleaning/Transforming:** As mentioned before, this is where unnecessary columns will be dropped and everything will be standardized. This step will be down using python and SQL.

**Data Mart:** The cleaned data will be structured into a star schema I have modeled in DbSchema, which will include a fact table for crashes and dimensions to help analyze the crashes meaningfully.

**Data warehouse:** Finally, the data mart will be loaded into the final data warehouse. The project will only use one singular data mart, but I chose this approach for flexibility in expanding the project in the future. At this final stage, users can access the data warehouse and generate reports.

### **Technical Architecture**

![1](https://github.com/user-attachments/assets/bbb0281d-7a1c-44e9-ac62-fcd52a01edba)

The planned technical architecture flow of the project begins with the Austin Crash Database, where the data is extracted via a python extract script.

The code stores the data straight into Microsoft Azure blob storage. 

A python ETL script is used afterward to perform the ETL, and loads the cleaned processed data into a Snowflake data warehouse.

After this step, the data is visualization ready and used to make a PowerBi dashboard.

### **Dimension modeling:**

![starschema](https://github.com/user-attachments/assets/cd0948e7-06f9-4cd1-ad54-aebb57337f4a)

**Fact table:**

The crashes table, which contains all counts and costs for the crash dataset.

**Dimension tables:**

Dim_TxDot, which contains whether the crashes took place on a txDot highway, a private driveway, or a construction site.

Dim_Severity, which denotes the crash severity level, from 0-5.

Dim_Location, which is all the location data for the crashes.

Dim_Calendar, which contains data for the crashes date, time, and whether the crashes took place on a holiday or weekend.
 
**Power BI Dashboard:**

![visualizations](https://github.com/user-attachments/assets/75717f89-5030-4b2c-bad6-38b6e04d7207)

Includes the following visualizations to support insights gained from the data:

Map Visual (crash severity across Austin by lat/long)

Slicer for filtering by crash year and severity

Pie Chart (distribution of crashes by severity)

Column Chart (crashes by year)

Line Chart (cost trends over time)

Heat Map (crash count by weekday and time-of-day)
