
## Table of Contents
- [Project Overview](#project-overview)
- [Excell Pivot Table](#excel-pivot-table)
- [Tableau Interactive Dashboard](#tableau-interactive-dashboard)
- [Data Sources](#data-sources)
- [Tools Used](#tools-used)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis ](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [ Reccomendations](#reccomendations)
- [  Limitations](#limitations)
- [References](#references)

---
### Project Overview
Create a comprehensive sales Dashboard for the Executive Team at a theoretical Bicycle reader company i work for from 2016-2018,The management team want to know-:
- Revenue per region
- Revenue per store
- Revenue per product category
- Revenue per Brand
- A list of top customers and sales

---

### Excell Pivot Table 
![9239](https://github.com/user-attachments/assets/a4ddbb19-7408-4165-b219-9844d3eb1269)

---



### Tableau Interactive Dashboard

![9208](https://github.com/user-attachments/assets/06ffa572-7ec2-4610-8e5b-a68511f81392)

---

### Data Sources
- Sample Data: Fictional sales, product, and customer data generated for analysis.
- Database Schema: Designed and populatedstore
- Revenue per product  category
- Revenue per brand
- A list of the top customers and sales using SQL scripts.

---

### Tools Used
- SQL Server-Data Exploration [Download here](https://instagram.com/damianmultipurpose)
- Excell-Pivot tables and charts for interactive Sales Dashboards.  [Download here](https://instagram.com/damianmultipurpose)
- Tableau-Interactive dashboards and data visualization.  [Download here](https://instagram.com/damianmultipurpose)

---
### Data Cleaning/Preparation
The data used in this project was clean and consistent throughout all stages of the analysis. No unwanted, redundant, or missing values were present. Data integrity was maintained when processed in SQL Server, imported into Excel, and further visualized in Tableau Public, ensuring accurate and reliable analysis.

---
### Exploratory Data Analysis
EDA involved exploring the sales data to answer key questions scuch as:

1.Which products are driving the most revenue?

2.What are the purchasing trends across different customer demographics?

3.Are there seasonal fluctuations in sales?

4.Which store locations are performing the best?

---
### Data Analysis
Include some interesting codes/features worked with
```sql
-- Select the necessary information to analyze customer orders, product sales, and store performance
SELECT 
    ord.order_id,  -- Order ID to uniquely identify each order
    CONCAT(cus.first_name, ' ', cus.last_name) AS full_name,  -- Customer's full name for better readability
    cus.city,  -- City of the customer
    cus.state,  -- State of the customer
    ord.order_date,  -- The date when the order was placed
    SUM(ite.quantity) AS total_units,  -- Total units ordered in this particular order (sum of all items ordered)
    SUM(ite.quantity * ite.list_price) AS revenue,  -- Revenue from this order (total units * price per unit)
    pro.product_name,  -- Name of the product being purchased
    cat.category_name,  -- Product category (e.g., mountain bikes, road bikes)
    sto.store_name,  -- Name of the store where the order was placed
    CONCAT(staf.first_name, ' ', staf.last_name) AS sales_rep  -- Name of the sales representative who handled the order

-- Joining various tables to bring together information from multiple sources
FROM sales.orders ord
-- Joining the orders table with the customers table to get customer details
JOIN sales.customers cus ON ord.customer_id = cus.customer_id
-- Joining the order_items table to get details about the products in each order
JOIN sales.order_items ite ON ord.order_id = ite.order_id
-- Joining the products table to get product names and other details
JOIN production.products pro ON ite.product_id = pro.product_id
-- Joining the categories table to get the category of each product
JOIN production.categories cat ON pro.category_id = cat.category_id
-- Joining the stores table to get the store name where the order was placed
JOIN sales.stores sto ON ord.store_id = sto.store_id
-- Joining the staffs table to identify which sales representative handled the order
JOIN sales.staffs staf ON ord.staff_id = staf.staff_id

-- Grouping the data by key attributes to get an aggregate summary of the orders
GROUP BY
    ord.order_id,  -- Grouping by Order ID to ensure that each order is summarized individually
    CONCAT(cus.first_name, ' ', cus.last_name),  -- Grouping by full name of the customer
    cus.city,  -- Grouping by city of the customer for geographic analysis
    cus.state,  -- Grouping by state of the customer
    ord.order_date,  -- Grouping by order date to analyze order trends over time
    pro.product_name,  -- Grouping by product name to see which products are selling the most
    cat.category_name,  -- Grouping by product category to identify top categories
    sto.store_name,  -- Grouping by store name to compare sales performance across stores
    CONCAT(staf.first_name, ' ', staf.last_name)  -- Grouping by sales representative to track performance by staff;
```


---
### Results/Findings**
The analysis results are summarised as follows:
    1.Top Revenue Drivers:
        Mountain Bikes and Road Bikes were the highest contributors to revenue.
    2.Customer Trends:
        Urban customers showed a preference for Road Bikes, with a significant share of total purchases.
    3.Seasonality in Sales:
        Sales consistently peaked during summer, indicating strong seasonal demand.
    4.Store Performance:
        One store outperformed others, attributed to better staff performance and customer engagement.
    5.Product Categories:
        Accessories and apparel had lower revenue compared to bikes but showed steady growth over time.
        
        ---
### Reccomendations****
1.Inventory Management:

    Increase stock for high-demand products like Mountain Bikes and Road Bikes during peak seasons.

2.Marketing Strategy:

    Target urban customers with promotions on Road Bikes and complementary accessories.

3.Seasonal Campaigns:

    Launch marketing campaigns during spring to maximize summer sales potential.

4.Store Performance Optimization:

    Analyze staff performance and replicate successful strategies across underperforming stores.

5.Diversification:

    Explore opportunities to expand product offerings in underperforming categories like accessories and apparel.


---
### Limitations****
1.Sample Data:

    The analysis is based on fictional sales data, which may not accurately reflect real-world scenarios or business operations.

2.Data Scope:

    The dataset covers only a limited timeframe (2016–2018) and does not include more recent or predictive trends.

3.Customer Demographics:

    Limited demographic information about customers restricts the ability to perform deeper behavioral analysis.

4.Seasonality Assumptions:

    Seasonal trends observed are specific to the dataset and may not generalize to other years or locations.

5.Static Data:
The data is static, and no real-time updates or dynamic queries were implemented in this analysis.

---

### References****

 [Work The Data Youtube Channel](https://www.youtube.com/watch?v=1pHYKdyRvrw&t=3748s)
 
 [@AlexTheAnalyst Youtube Channel](https://www.youtube.com/@AlexTheAnalyst)
 
 [CHATGPT.COM](https://chatgpt.com/c/675ca5f8-6bd8-8002-bfab-a677b20c933c)
