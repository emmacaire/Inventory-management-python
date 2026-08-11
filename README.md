# Inventory Management Healthcare
**Source Data:** Kaggle [link](https://www.kaggle.com/datasets/abdallahwagih/telco-customer-churn) / Download Source Data [Inventory Transactions](https://github.com/emmacaire/Inventory-management-python/blob/main/source/Inventory_Transactions.csv) [Product Master List](https://github.com/emmacaire/Inventory-management-python/blob/main/source/Product_Master_List.csv)
<br>
<br>

## 📌 Summary
....With this project I developed a realistic Business Intelligence workflow from enterprise data collection to the delivery of a Power BI report and data-driven future prescriptive actions. Data involved customer transactions as contract renewal, new contract or end of contract. In case of churn, additional data on the reason why the customer interrupted the contract was collected.

Python was used early in the project to manipulate the data and allow for a proper date dimension, other than for quick data cleaning and checks.
Afterwards the manipulated source data was loaded in Fabric, first in a Staging Area, then in the proper Data Warehouse, after complying with quality checks performed in a pipeline through scripts and stored procedures. There was extensive use of dataflows and pipelines during these stages, other than the SQL scripts to load the final dimensional model.
Once the clean data was loaded in the warehouse, a Snowflake schema was created in the semantic model and the most relevant insights were presented in a 5-dashboard report. 

A more detailed description of the project phases follows in the Project Details section.
<br>
<br>

## 📊 Key Deliverables & Artifacts
* 📂 **Interactive Power BI report:** Download the interactive .pbix [report](./powerbi/RP_Telco_Churn_stored.pbix/) file to open and interact with the full dashboard in Power BI Desktop.
* 📄 **Project Source Code:** View the .pbib [folder](./powerbi/RP_Telco_Churn_stored.pbip/) to inspect the underlying DAX measures, TMDL model definitions, and report metadata tracked via Git. 

![Dashboard Preview](./assets/dashboard-demo.gif)

## 🛠️ Tech Stack & Methodology
* **Softwares and platforms:**
  - Power BI Desktop (semantic model, Snowflake schema, DAX, report),
  - Power Automate (button to send an automated e-mail with an HTML table)

* **Visualization tools:**  bar charts, donut charts, decomposition trees, multi-level cards, KPIs, slicers, interactive maps, scatter plots, navigation buttons, custom tooltips.
<br>
<img width="1452" height="816" alt="RP_pg1" src="https://github.com/user-attachments/assets/90af4680-2dc9-45d0-8d8d-7e967279232a" />
<br>
<br>
<img width="1455" height="820" alt="RP_pg4" src="https://github.com/user-attachments/assets/fba39242-e86a-444b-b8a6-ba7247f9de7b" />
<br>
<br>

## 📋 Project Details
<br>
<br>
<ins>Phase 1: Source Data selection</ins>
<br>
<br>
The Telco California Churn dataset is large enough in terms of rows (7000+) and offers a wide range of variables and categories that allow to build a solid dimensional model, including longitude and latitude to support map views. However, it presents a fundamental limitation, reporting only Q3 2025 transactions with no precise date. This would not allow the creation of a proper dimension date and I would miss several opportunities for analysis. For this reason, I opt for a quick manipulation in Python that generates an artificial date column.
<br>
<br>
<img width="1286" height="246" alt="image" src="https://github.com/user-attachments/assets/05a4923b-0dc4-445c-a518-6162f79cc121" />
<br>
<br>
<br>
<ins>Phase 2: Source Data manipulation in Python</ins>
<br>
<br>
In Python I create a column with randomly generated dates for the whole 2025 year. This will likely give very flat distributions, without significant peaks or patterns, but I am curious to confirm this intuition after the analysis.
In addition I check for outliers, data entry errors (negative values or out of scale values), missing data, etc.
The code to create random dates looks like this:
<br>
<br>
<img width="522" height="152" alt="ph2_python" src="https://github.com/user-attachments/assets/1c40bc5d-f0ca-409e-8ca5-b4661788f952" />
<br>
<br>
<br>
<ins>Phase 3: Loading the Source Data in Fabric (Bronze Layer)</ins>
<br>
<br>
Now that the source data has been improved, it is imported in Fabric as the Bronze layer in CSV format, and stored in a Lakehouse. From this one, I will use dataflows to transform the source table and load the dimension and fact tables.
<br>
<br>
<br>
<ins>Phase 4: Select the business questions and define the dimensional model </ins>
<br>
<br>
Once the source data is available, the most important of all steps is to understand what do I need the data to tell me, and how I will get the information. This is the moment where I understand how much can be asked to this dataset. The date and geographical coordinates suggest that Date and Location dimensions would be very helpful to extract patterns in terms of time/space. In addition, each transaction is linked to a customer code, which will be crucial for profiling the type of user, adding a Customer dimension. In addition, two columns in hierarchical sequence define whether the user has internet and if so, what type it is. Thus I will add a Service category, while all the other binary variables on other types of service are not linked to each other in a hierarchical way, therefore they will left as attributes. Each transaction other than the customer status (stayed, churned, joined) also contains important measures: the tenure in month and the charge in USD are important metrics that relate to the customer status. The sort of questions that the model will allow to answer is this:
- did the company have more customers joining or leaving?
- how does geography affect churn and the related metrics?
- is the churn rate changing throughout the year? (unlikely given that the column was generated artificially)
- what motivation did the customer indicate as main reason for churning?
- are demographic and product features distributed differently across customer that stayed, churned and joined?
- is there a relationship between metrics, such as tenure in months and customer charge?
<br>
The defined model was a Snowflake schema where Fact Customer Transactions would link to Dimension Date and Dimension Customer, while Dimension Customer would further connect to Dimension Service and Dimension Location.
<br>
<br>
<br>
<ins>Phase 5: ETL in Fabric (Silver Layer)</ins>
<br>
<br>
The defined dimensions and fact tables are created with an [SQL query](.sql/sql_load_staging_area.sql) and data is ingested there through [dataflows](.dataflows/Dataflows_Telco_Churn.txt).
Dataflows commands include selecting the appropriate columns, generating new columns that build other categories on top of the existing ones, cleaning the dimension rows by removing duplicates, and ensuring that the primary and foreign keys are selected appropriately.
Once all the dataflows are ready I create a pipeline that will load all the data from the in a Staging Area warehouse, where some quality checks will be performed before loading into the final data warehouse.
<br>
<br>
<img width="1566" height="335" alt="PL_load_staging_area" src="https://github.com/user-attachments/assets/67ac9cd7-9145-4ed6-96ac-e5e11d178da3" />
<br>
<br>
Another pipeline is created to perform quality checks including the integrity of the business key, the uniqueness of dimension attributes, no negative values in the charge column, and the prensence of the parent key for each child table referencing other foreign keys. Those checks are done through direct [scripts](.sql/sql_create_log_table_quality_checks.sql) in the pipeline or through [stored procedures](.sql/sql_stored_procedures_quality_checks.sql). 
<br>
<br>
<img width="587" height="542" alt="PL_log_quality_checks" src="https://github.com/user-attachments/assets/5d16bdd1-6deb-4862-b070-c023454d67e4" />
<br>
<br>
The results, after the pipeline is run, are visible in the quality checks table in the staging area warehouse.
<br>
<br>
<img width="1237" height="257" alt="ETL_quality_checks" src="https://github.com/user-attachments/assets/f9d25646-03c2-43a8-8bde-e315ff4bb82e" />
<br>
<br>
After having checked the data in the staging area, I can finally transfer my table content to the final Data Warehouse in the Gold Layer.
<br>
<br>
<br>
<ins>Phase 6: Loading the Data Warehouse in Fabric (Gold Layer)</ins>
<br>
<br>
With another pipeline, I can now fill the Data Warehouse with the filtered and cleaned data from the Staging Area data warehouse. The key focus in the pipeline is on ensuring that each dimension table is loaded with an additional progressive surrogate key, other than the business key. It's the surrogate key that will reference the dimension as a foreign key in the fact table, instead of the business key.
<br>
<br>
<img width="1497" height="411" alt="PL_load_data_warehouse" src="https://github.com/user-attachments/assets/44d6f33e-538b-46a2-bab9-1ac70d353b47" />
<br>
<br>
<br>
<ins>Phase 7: Creating the relational model in Power BI</ins>
<br>
<br>
At this stage I create the semantic model in Power BI and link tables with appropriate relationships. I additionally perform some changes that will help during the creation of the report:
* set Dimension Date as the Date reference table,
* create the date hierarchy,
* order the text version of the Weekday and Month by the corresponding numeric column, for them to be displayed in logical order, rather than in alphabetical one.
* rename fields so they look more synthetic and visually appealing on the dashboards,
* hide fields of surrogate and business keys, since they will not be used in the visuals.
<br>
<br>
<img width="1037" height="512" alt="image" src="https://github.com/user-attachments/assets/c742d2ce-e7f6-4259-9a3d-f6ecaaf027f3" />
<br>
<br>
<br>
<ins>Phase 8: Creating additional measures (DAX)</ins>
<br>
<br>
Time has come to identify which other measures would be interesting for the report. This includes count of total rows, average values, % ratios, Month-over-Month growth, etc.
A few examples of the calculated measures created at this stage:
<br>
<br>
Total Transactions = COUNTROWS('fact_customer_transactions')
<br>
Churn Transactions = CALCULATE(COUNTROWS(fact_customer_transactions),fact_customer_transactions[Status]="Churned")
<br>
Churn Rate = DIVIDE ([Churn Transactions],[Total Transactions],0)
<br>
Churn Losses = [Avg Charge Churned] * [Churn Transactions] * (-1)
<br>
% Churned Losses = DIVIDE([Churn Losses],[Total Revenue],0)
<br>
<br>
<br>
<ins>Phase 9: Selecting key insights and creating the report</ins>
<br>
<br>
The report is made by 5 dashboards, as displayed at the bottom of this page. Buttons on the left allow to easily switch from one to another dashboard.
<br>
Brief description of the dashboards:
<br>
<br>

**1. Churn Overview**: a list of KPI on churned and joined customers and a summary of the main reasons indicated by customers when asked why they decided to cancel their contract, in order to immediately capture the general situation.
<br>

**2. Demographic comparison**: a comparison of key metrics and demographic attributes between customers who stayed and those who churned, to understand which ones present significant differences between the two sub-groups and which not.
<br>

**3. Product comparison**: an extension of previous analysis but focusing on product characteristics rather than customer profile, to capture the different products that customers were using when deciding to opt out or renew.
<br>

**4. Churn Map**: a dashboard capturing all relevant geographical differences, with two charts focusing on main cities and a full map where the slicer can filter by the reason for churning, and the data points are further labeled by length of tenure in months (color of the dot) and monthly charge (size of the dot).
<br>

**5. Time Series:** it displays the most interesting month-over-month evolution in % compared to previous month, and two further stacked bar charts that cross check motivation for churning by month, and churn in large cities by quarter, capturing significant differences.
<br>
<br>
<br>
<ins>Phase 10: Summarizing prescriptive actions</ins>
<br>
<br>
After viewing the report, the users should take their own conclusions based on their business knowledge. 
On the basis of my limited knowledge of the market and the company, I list some insights - as a combination of observation and suggested action - that should be prescribed in order to improve  business strategy:
<br>
<br>
**INSIGHT #1**
<br>
- *OBSERVATION:* there is an urgent need to address the high amount of lost customers during 2025 (30%!), which is not compensated at all by the number of new customers. Competitors seem to be performing aggressive strategies that convince users to change, more than any other reason.
- *ACTION:* immediately compare the products with those of competitors and evaluate more effective strategies for customer retention, including discounts and special offers.
<br>

**INSIGHT #2** 
<br>
- *OBSERVATION*: looking at demographics and product comparison, the customer with higher likelihood to churn is more price sensitive (paperless billing, shorter tenure average, higher average charge) and with more time to compare offers (older age, no dependencies). The predominance of bank withdrawal as payment method among churning customers confirms a higher age, as credit cards are more used among younger people. Churning customers also have less premium support but more unlimited data contracts. Offer E seems to be the only one that is still active.
- *ACTION:* An accurate clustering of the different customers might help identify which categories are more fragile and prone to churning. The difference in terms of unlimited data and premium support between staying and churning customers should also be investigated, they might simply reflect different product policies in the latest years. For instance, unlimited data might have been included as standard plan in the latest years and could explain why churning customers, who have on average a shorter past tenure in months, have more unlimited data plans than those who stayed, who have also been customers for longer time and with older offers. This would also explain why Offer E is so unpopular among churning customers, yet it is the only available offer for new customers. At first glance, it seems as a substantial amount of customers might prefer offers with less features but more competitive in terms of pricing.
<br>

**INSIGHT #3** 
<br>
- *OBSERVATION:* there are relevant differences in geographical areas. San Diego area is causing massive churn due to high charges. Fresno should be taken as a model for being able to hold relatively high charges and longer tenure in months.
- *ACTION:* analyze the situation in these different markets and understand if there are specific barriers that prevent the benchmark areas to be replicated in the most problematic areas, especially San Diego. For example, competition might be less fierce in Fresno compared to San Diego, which would explain why the company is doing better there.
<br>

**INSIGHT #4**
<br>
- *OBSERVATION:* there are limited variations in terms of time of the year. There does not seem to be a particularly vulnerable period of the year where churn increases, however patterns are slightly different across towns, for instance in San Francisco and Fresno churn seems to be more likely in the latest two quarters, while in Oakland it mostly happens in Q1 and Q2. 
- *ACTION:* the company should focus on an all-year-round strategy rather than specific ones in certain key periods. Customers seem to churn at any time with no particular peaks of dissatisfaction. (note: dates were generated artificially and randomly for this project's purpose, as explained in phase 2 !).
<br>
<br>
Full report preview:
<br>
<br>
<img width="1452" height="816" alt="RP_pg1" src="https://github.com/user-attachments/assets/6ee70fec-c04c-4c7b-b2de-62c74e736246" />
<br>
<br>
<img width="1312" height="737" alt="RP_pg2" src="https://github.com/user-attachments/assets/d47466fd-6867-4eeb-98aa-db6b7d00454e" />
<br>
<br>
<img width="1312" height="732" alt="RP_pg3" src="https://github.com/user-attachments/assets/e994492c-f91b-4367-85af-24a6db45f0f4" />
<br>
<br>
<img width="1455" height="820" alt="RP_pg4" src="https://github.com/user-attachments/assets/e896e3fd-0e3e-41fa-aa09-bb1fd3d773ec" />
<br>
<br>
<img width="1312" height="736" alt="RP_pg5" src="https://github.com/user-attachments/assets/0c8da78e-3cc8-4601-a989-620749cf0a41" />

