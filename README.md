# Restaurant-Sales-Performance-SQL-Analytics
An end-to-end data analysis project exploring a dataset of 100,000+ restaurant transactions using advanced SQL queries. The goal is to uncover actionable business insights regarding revenue distribution, customer payment preferences, city-wise performance, and top-selling menu items to support data-driven decision-making

## Business Problems & SQL Solutions
```sql
1. City Financial Performance
* Business Problem: Calculate total rounded bills and gross profit per city, sorted by lowest total bill first, then highest gross profit.
* **SQL Query:**<img width="279" height="116" alt="p1" src="https://github.com/user-attachments/assets/b5fbecbe-ad1b-4f46-ade6-52e9e2d4a027" />
SELECT 
    Restaurant_City,
    ROUND(SUM(Total_Bill),0) AS Total_Bill,
    ROUND(SUM(Gross_Profit),0) AS Gross_Profit
FROM rest
GROUP BY Restaurant_City
ORDER BY 2, 3 DESC;

![p1](https://github.com/user-attachments/assets/9ef7c28d-9738-4d38-8b6a-392441dde520)

2. Payment Method Insights
Business Problem: Find the total rounded bill and total order count for each payment method, sorted from highest to lowest total bill.

SELECT 
    Payment_Method,
    ROUND(SUM(Total_Bill), 0) AS Total_Bill,
    COUNT(Order_ID) AS Orders
FROM rest
GROUP BY Payment_Method
ORDER BY Total_Bill DESC;

3. Top 5 Best-Selling Items
Business Problem: Find the top 5 highest-selling menu items across all categories based on total quantity sold.

SELECT 
    Category, Item_Name, SUM(Quantity) AS Qun
FROM rest
GROUP BY Category, Item_Name
ORDER BY Qun DESC
LIMIT 5;

4. Platform Performance
Business Problem: Find the total order count and rounded total bill for each ordering platform, sorted by lowest orders first, then highest total bill.

SELECT 
    Platform,
    COUNT(Order_ID) AS orders,
    ROUND(SUM(Total_Bill), 0) AS Total_Bill
FROM rest
GROUP BY Platform
ORDER BY orders, Total_Bill DESC;

5. Revenue Share by City
Business Problem: Calculate each city's percentage share of the overall total revenue.

SELECT 
    Restaurant_City,
    ROUND(SUM(Total_Bill) / (SELECT SUM(Total_Bill) FROM rest) * 100, 1) AS revenue
FROM rest
GROUP BY 1;

6. Top Item per City (Window Functions)
Business Problem: Find the highest-selling menu item (top-ranked by quantity) for each restaurant city.

SELECT Restaurant_City, Item_Name, ranks, quan
FROM (
    SELECT Restaurant_City, Item_Name, 
    SUM(Quantity) AS quan,
    RANK() OVER (PARTITION BY Restaurant_City ORDER BY SUM(Quantity) DESC) AS ranks
    FROM rest
    GROUP BY 1, 2
) AS a
WHERE ranks = 1;

7. Above-Average Selling Items (Subqueries & HAVING)
Business Problem: Find all menu items whose total quantity sold is greater than the overall average quantity sold across all items.

SELECT Item_Name, SUM(Quantity) AS Total_Quantity
FROM rest
GROUP BY Item_Name
HAVING SUM(Quantity) > (
    SELECT AVG(item_total) 
    FROM (
        SELECT SUM(Quantity) AS item_total 
        FROM rest 
        GROUP BY Item_Name
    ) AS sub
)
ORDER BY Total_Quantity DESC;

Language: SQL
