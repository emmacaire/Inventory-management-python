# Inventory Management Healthcare
**Source Data:** Kaggle [link](https://www.kaggle.com/datasets/nevinfritsch/medical-supply-chain-and-inventory-risk-analysis) / Download source files [Inventory Transactions](https://github.com/emmacaire/Inventory-management-python/blob/main/source/Inventory_Transactions.csv) [Product Master List](https://github.com/emmacaire/Inventory-management-python/blob/main/source/Product_Master_List.csv)
<br>
<br>

## 📌 Summary
The analysis focused on inventory data in a pharma company, offering a wide range of insights on inventory status, re-ordering practices, branch and staff performance, ABC stratification and pricing strategies. There were two main limitations of this dataset:
  1. data included all outbound transactions of ordered material, but lacked inbound transactions, i.e. purchase orders made throughout the year to replenish stock. Basically it only includes what leaves the warehouse (sales) without registering what enters the warehouse (purchasing). For this reason, the analysis on the purchasing side was only static, focused on current stock, while for the sales side, it was possible to inspect the trend throughout the considered time period.
  2.  there was no indication of the unit price for each item, only the unit cost. This meant that most of the analysis was focusing on revenue at cost of purchasing, while revenue on selling price was only studied as a simulation, analyzing the possible outcomes of different price markups.

The analysis was conducted entirely on Power BI Desktop, with extensive use of Power Query for the ETL part and a simple relational model with one fact table and two dimension tables (product master list and date) in the semantic model. The main focus in this project was on the creation of complex DAX formulas that would support inventory management.
The final output is a Power BI report consisting of 7 connected dashboards, including a Power Automate button to notify the required stock replenishment.

A more detailed analysis of the DAX formulas, the dashboard purposes and the derived insights is available below.
<br>
<br>

## 📊 Key Deliverables & Artifacts
* 📂 **Interactive Power BI report:** Download the interactive .pbix [report](./report/Inventory_management.pbix) file to open and interact with the full dashboard in Power BI Desktop.
* 📄 **Project Source Code:** View the .pbib [folder](./report/Inventory_management.pbip) to inspect the underlying DAX measures, TMDL model definitions, and report metadata tracked via Git. 

![Dashboard Preview](./assets/dashboard-demo.gif)

## 🛠️ Tech Stack & Methodology
* **Softwares and platforms:**
  - Power BI Desktop (semantic model, star schema, DAX, report),
  - Power Automate (button activating an automated e-mail with an HTML table)

* **Visualization tools:**  KPIs, multi-level cards, bar charts, mixed bar/line charts, decomposition trees, matrixes, slicers, scatter plots, field parameters, navigation buttons, custom tooltips, Power Automate buttons.
<br>
<br>
<img width="1417" height="795" alt="s02" src="https://github.com/user-attachments/assets/86c5ac8e-c4e1-4654-83e0-5e0631777a8a" />
<br>
<br>
<img width="1420" height="797" alt="s06" src="https://github.com/user-attachments/assets/004eb43f-f6fe-4e22-90f6-2d8418a39aa6" />
<br>
<br>

## 📋 Project Details
<br>
<br>
<ins>Semantic model</ins>
<br>
<br>
The model consists of a simple star schema where product and time dimension refer to the inventory transaction table through their respective foreign keys.
Two hierarchies have been created, one in the product category and one drilling down from year to quarter and month.
<br>
<br>
<img width="1175" height="708" alt="Screenshot 2026-08-11 170854" src="https://github.com/user-attachments/assets/1f674f8e-bd7d-42dd-a113-175e8b696ffa" />
<br>
<br>
<br>
<ins>DAX formulas</ins>
<br>
<br>
xxx
<br>
<br>
<br>
<ins>Dashboards</ins>
<br>
<br>
The report consists of 7 dashboards. Buttons on the left allow to easily switch dashboard while consulting the report.
<br>
Here is a description of each dashboard:
<br>
<br>

**1. Overview on Stock Health**: this executive summary displays the main KPIs at the top, followed by the status of each individual item in stock, in red if stock is sufficient and red if it needs replenishment. There is also a label for those items that are overstocked. Additional graphs also illustrate the situation in terms of broad item category and detailed item description.
<br>
<br>
<img width="1421" height="797" alt="s01" src="https://github.com/user-attachments/assets/5a66e8a6-a10a-41f0-8885-192560923368" />
<br>
<br>
**2. Reorder Trigger**: an immediately actionable page that calculates which and how many items should be purchased to replenish stock. With Power Automate a simple button allows to send an email to the responsible individuals, listing item codes, supplier name, quantities, unit and total costs that should be included in the next purchase order. Additional charts show the risk level of the missing items and their lead time.
<br>
<br>
<img width="1417" height="795" alt="s02" src="https://github.com/user-attachments/assets/a344dfc1-6d0c-45c2-8d7f-8de9ad8bcced" />
<br>
<br>
**3. Demand Velocity and Stock Burn Rate**: with a single choice filter, for product at a time we are able to see the total quantities sold and daily burn rate throughout the year, other than key metrics such as current stock level, average daily consumption and days of supply remaining. This chart highlights a fundamental issue of the current inventory structure where we notice that items with least days of supply remaining also have larger lead time. More detail in the Insights section at point # 1.
<br>
<br>
<img width="1417" height="797" alt="s03" src="https://github.com/user-attachments/assets/1b93e3b7-a719-453f-923c-d449adb038d0" />
<br>
<br>
**4. Regional Performance**: a dashboard capturing all relevant geographical differences across the four branch that order parts from the warehouse. Some are performing better and some worse, but overall we notice that differences are not significant. In addition, we can see that orders follow certain patterns, as each branch's top order has the same amount, which suggests strict purchasing procedures.
<br>
<br>
<img width="1421" height="800" alt="s04" src="https://github.com/user-attachments/assets/9128179c-b5ee-40fa-84d1-6ebdb2be44ff" />
<br>
<br>
**5. Staff Performance:** very similar to the previous branch chart, but covering the composition across the different staff members ordering parts instead. I would have expected much more difference in terms of parts ordered between the three opticians and the surgeon, but their behavior is very similar, which is unconvincing in terms of source data.
<br>
<br>
<img width="1421" height="797" alt="s05" src="https://github.com/user-attachments/assets/304506b9-d776-4003-8c6d-6c987b3c2b13" />
<br>
<br>
**6. Financial ABC Stratification:** the dashboard shows the results of the Pareto analysis and the distribution of each item into the respective ABC class. The most valuable products are surgical items, while contact lenses and solutions are cheaper. The supplier concentration donut chart also shows this pattern, where the almost even distribution across the four main categories in terms of quantity changes completely if instead of considering the quantities sold we look at the associated revenue.
<br>
<br>
<img width="1420" height="797" alt="s06" src="https://github.com/user-attachments/assets/e7ba3fdf-9c97-4bd6-b9c0-30eced4d38a9" />
<br>
<br>
**7. Pricing & Profit Simulation:** since the source data is not stating what the selling price is, we can create a dashboard that allows to select possible scenarios and determine the relative profit based on the defined markups. A base markup on different product categories is set, then an additional price markup can be defined, and the charts show the resulting retail revenue and gross profit %.
<br>
<br>
<img width="1417" height="797" alt="s07" src="https://github.com/user-attachments/assets/a29c7822-7339-41ea-9e86-2c3e6b301cc0" />
<br>
<br>
<br>
<ins>Summarizing prescriptive actions</ins>
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
<br>
