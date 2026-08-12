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

* **Visualization tools:**  KPIs, multi-level cards, bar charts, mixed bar/line charts, decomposition trees, matrixes, slicers, scatter plots, field and numeric parameters, navigation buttons, custom tooltips, Power Automate buttons.
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
In this section I summarize the most complex and relevant DAX formulas that I created for this project, explaining what the purpose of each one is.

**% SKUs below ROP**
<img width="881" height="231" alt="image" src="https://github.com/user-attachments/assets/b220a7cd-eccd-458b-a9c0-3e8151621a5d" />

For each row in the Product Master List, i.e. each distinct item, the formula evaluates with 1 if the stock level is below reorder point, with 0 if not, then divides by the total products in the master list to have the % of product types that need reorder.

**Cumulative %**

<img width="573" height="455" alt="image" src="https://github.com/user-attachments/assets/e9232643-d301-406a-a770-0988956fb9a3" />

This measure is used to create the cumulative % of each item type for the Pareto Chart. First I need to set up the cumulative revenue value (CumulativeRevenue) as the total revenue of the considered item, but adding up the revenue from all other items which have a higher revenue than this one! Afterwards, the obtained value is divided by the total revenue of all the items together (TotalAllRevenue). This way, for the higher revenue item, no other revenue will be added, while for the lowest revenue item, all other revenue values will be summed, so the cumulative value will equal the total revenue and the result will be 100%, as expected.

**Daily Burn Rate**

<img width="637" height="182" alt="image" src="https://github.com/user-attachments/assets/feac7510-1743-4702-b985-20da45e63a90" />

If I calculated the burn rate using day-to-day values, the chart would be fluctuating in a way that it would not be readable and usable. By smoothing the curve taking account the dynamic burn rate, changing every day, but over the past 30 days of each day, the curve is meaningful and more interpretable, giving us an idea of whether stock is increasing or decreasing in any time range. To calculate this, we average the daily sum of sold quantities in the past 30 days in each date of the year, and set this average as value for that specific day. Each consecutive day will have a slightly different value, because the value of the oldest day was replaced by the value of the new day.

**MoM Qty Change %**

<img width="985" height="117" alt="image" src="https://github.com/user-attachments/assets/b4261dc7-bcd2-439b-b91c-511f40bcaead" />

A classic dynamic measure comparing each month with the respective previous one, and summarizing totals as % variation over the previous month. The use of variables allows to simplify the readability of the final formula.

**Required Stock**

<img width="882" height="117" alt="image" src="https://github.com/user-attachments/assets/2db3e6e2-1da5-4484-ac0f-c78c249b16c3" />

The formula compares the stock level for a particular product with its corresponding reorder point. When the condition is met, i.e. stock is lower than reorder point, it returns the stock balance value in positive value, as number of units that are missing. When the condition is not met, it stays blank. This way the reorder trigger table is filled with only those categories that need stock replenishment. The same formula is used in a separate measure replacing the value of the products instead of the quantity, to obtain the total cost of the required order.

**Simulated Retail Revenue**

<img width="656" height="317" alt="image" src="https://github.com/user-attachments/assets/49c9504a-a50c-485e-8680-c71a6d303582" />

After selecting the numeric values in the parameters for markups in the Price Simulation section, this is the formula that summarizes every input and produces the simulated revenue that would be produced with such price settings. First of all, the base markup is defined as the selected option in the base markup for surgical items, the selected option in the frames items base markup, and a fix 1.25 markup for the other categories, i.e. contact lenses and solutions. The SWITCH function allows to evaluate each product by Category in sequential order, and if it does not belong to the first listed category (surgical) nor the second (frames), it keeps the residual markup of 1.25. After the BaseMarkup variable, another variable is set to get the input of the Price Markup % parameter. Finally, the revenue is calculated as revenue at cost multiplied by the sum of the two levels of markup, the base one which is category-specific, and the general one called Price Markup %.

**SKU Stock Status Message**

<img width="811" height="496" alt="image" src="https://github.com/user-attachments/assets/fd94ba99-853d-4d33-a67d-b7049bbb4773" />

The formula is used to label each product stock as "Overstocked", "Healthy Stock" or "Reorder Needed". The formula compares the stock with the reorder point and produces the three different outcomes with a SWITCH formula that sequentially defines the possible outcomes of the condition. If the stock is equal or below reorder point, a new purchase order is needed. If stock is more than twice the reorder point, the product is considered to be overstocked. In any other scenario, i.e. stock above reorder point but less than twice, the stock level is healthy.

**Top Revenue SKU Info**

<img width="611" height="201" alt="image" src="https://github.com/user-attachments/assets/b297e9be-e7b5-4a97-8014-6520bf5f3c32" />

A simple concatenation of different values to be displayed in a card. In sequence, the card will take the top ranked item in the Product Master List, by revenue, and will display the SKU code and total revenue produced by this item.
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
On the basis of my knowledge of the market and the company, I list some insights - as a combination of observation and suggested action - that should be prescribed in order to improve  business strategy:
<br>
<br>
**INSIGHT #1**
<br>
- *OBSERVATION:* there is a pattern of correlation between items with longer lead time and low reorder point. This makes sense in terms of cost optimization but can be dangerous when buffers in delivery can cause the warehouse to run out of stock. This is the case of "Intraocular Lens" and "Phaco Tip", for instance. These two have respectively 2 and 5 days of supply remaining, so unless orders were placed 43 and 25 days ago, the company is likely to run out of stock soon, with current average daily consumption values.
- *ACTION:* urgently determine whether purchase orders for the most critical components have been placed and if delivery is predicted to be on time. Afterwards, we should determine if and how many times in the past these critical components with long lead time have arrived late leaving the warehouse out of stock. Based on that, reorder points might be re-defined so the right buffer is provided.
<br>

**INSIGHT #2** 
<br>
- *OBSERVATION*: the good news is on the trend over the year. There does not seem to be a particularly pronounced seasonal trend for any product, but only fluctuations due to specific orders. The same goes for variation across staff and location. This is a good news because it makes supply chain needs more predictable than other markets, and justify lower reorder point values in general.
- *ACTION:* if we could add data from previous years than 2025 as well, this could reinforce our analysis and confirm whether trends were very predictable over the previous years as well.
<br>

**INSIGHT #3** 
<br>
- *OBSERVATION:* our Pareto Chart shows a clear hierarchy between product categories. Surgical items are clearly the most important ones, followed by frames, with contact lenses and solutions as least important ones. This is an important insight in our value creation process and reflects on the pricing choice; we add more base markup to the most valuable items that are also more complex and harder to gather from alternative suppliers on the market.
- *ACTION:* a target gross profit of 60% (150% markup) seems reasonable but in this market there might be an opportunity to reach 80% (400% markup) in the higher end.
<br>
<br>
<br>
