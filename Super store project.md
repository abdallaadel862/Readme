# Readme
sql 
cleaning:SELECT * FROM customers_clean
SELECT * FROM orders_clean
SELECT * FROM products_clean
SELECT * FROM order_details_clean


SELECT * FROM customers_clean WHERE Customer_ID IS NULL
SELECT * FROM orders_clean WHERE Order_ID IS NULL
SELECT * FROM products_clean WHERE Product_ID IS NULL
SELECT * FROM order_details_clean WHERE Order_ID IS NULL OR Product_ID IS NULL

SELECT o.Order_ID ,c.Customer_Name
FROM orders_clean o
LEFT JOIN customers_clean c ON o.Customer_ID = c.Customer_ID
WHERE c.Customer_ID IS NULL

SELECT od.Order_ID
FROM order_details_clean od 
LEFT JOIN orders_clean o ON od.Order_ID = o.Order_ID
WHERE o.Order_ID IS NULL

SELECT od.Product_ID
FROM order_details_clean od
LEFT JOIN products_clean p ON od.Product_ID = p.Product_ID
WHERE p.Product_ID IS NULL

SELECT COUNT(DISTINCT Customer_ID) AS UniqueCustomers, COUNT(*) AS TotalCustomers
FROM customers_clean

SELECT COUNT(DISTINCT Order_ID) AS UniqueOrders, COUNT(*) AS TotalOrders
FROM orders_clean

SELECT COUNT(*) AS JoinedCount
FROM order_details_final od
JOIN orders_clean o   ON od.Order_ID   = o.Order_ID
JOIN customers_clean c ON o.Customer_ID = c.Customer_ID
JOIN products_clean p ON od.Product_ID = p.Product_ID;

SELECT COUNT(*) AS OrderDetailsCount FROM order_details_clean;

-- KPI
--1total Sales

SELECT SUM(Sales) AS Total_Sales
FROM OrderDetails;

-- 2Unique Orders

SELECT COUNT(DISTINCT [Order_ID]) AS Unique_Orders
FROM Orders;

-- 3Unique Customers

SELECT COUNT(DISTINCT [Customer_ID]) AS Unique_Customers
FROM Customers;

-- 4Average Order Value (AOV)

SELECT
    (SELECT SUM(Sales) FROM OrderDetails) /
    (SELECT CAST(COUNT(DISTINCT [Order_ID]) AS FLOAT) FROM Orders) AS AOV;

-- 5Sales Trend Over Time (Month-over-Month)
SELECT
    FORMAT(o.[Order_Date], 'yyyy-MM') AS Month,
    SUM(od.Sales) AS Monthly_Sales
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
GROUP BY
    FORMAT(o.[Order_Date], 'yyyy-MM')
ORDER BY
    Month;

-- 6Average Items per Order (UPO / "Units Per Order")

SELECT
    CAST(COUNT(*) AS FLOAT) / (SELECT COUNT(DISTINCT [Order_ID]) FROM Orders) AS Avg_Items_Per_Order
FROM OrderDetails;



--2 Customer analysis


-- 1Top 10 Customers by Sales
SELECT TOP 10
    c.[Customer_Name],
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
JOIN
    Customers AS c ON o.[Customer_ID] = c.[Customer_ID]
GROUP BY
    c.[Customer_Name]
ORDER BY
    Total_Sales DESC;

-- 2Top 10 Customers by Order Count
SELECT TOP 10
    c.[Customer_Name],
    COUNT(DISTINCT o.[Order_ID]) AS Order_Count
FROM
    Orders AS o
JOIN
    Customers AS c ON o.[Customer_ID] = c.[Customer_ID]
GROUP BY
    c.[Customer_Name]
ORDER BY
    Order_Count DESC;

-- 3Customer Acquisition Trend (New Customers per Month)

SELECT
    FORMAT(Order_Date, 'yyyy-MM') AS Month,
    COUNT(*) AS New_Customers
FROM Orders
GROUP BY FORMAT(Order_Date, 'yyyy-MM')
ORDER BY Month;

-- 4Distribution of customers by Segment

SELECT
    Segment,
    COUNT(DISTINCT [Customer_ID]) AS Customer_Count
FROM Customers
GROUP BY Segment
ORDER BY Customer_Count DESC;


--3GEOGRAPHICAL ANALYSIS

-- 1Top 10 Cities by Sales
SELECT TOP 10
    o.City,
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
JOIN
    Customers AS c ON o.[Customer_ID] = c.[Customer_ID]
GROUP BY
    o.City
ORDER BY
    Total_Sales DESC;

-- 2 Bottom 10 Cities by Sales

SELECT TOP 10
    o.City,
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
JOIN
    Customers AS c ON o.[Customer_ID] = c.[Customer_ID]
GROUP BY
    o.City
ORDER BY
    Total_Sales ASC; 

-- 3 Sales by State
SELECT
    o.State,
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
JOIN
    Customers AS c ON o.[Customer_ID] = c.[Customer_ID]
GROUP BY
    o.State
ORDER BY
    Total_Sales DESC;

-- 4 Sales by Region

SELECT
    o.Region,
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
JOIN
    Customers AS c ON o.[Customer_ID] = c.[Customer_ID]
GROUP BY
    o.Region
ORDER BY
    Total_Sales DESC;



--4PRODUCT ANALYSIS


-- 1 Top 10 Best-Selling Products by Sales
SELECT TOP 10
    p.[Product_Name],
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Products AS p ON od.[Product_ID] = p.[Product_ID]
GROUP BY
    p.[Product_Name]
ORDER BY
    Total_Sales DESC;

-- 2 Top 10 Best-Selling Products by Count (Popularity)

SELECT TOP 10
    p.[Product_Name],
    COUNT(od.[Product_ID]) AS Total_Units_Sold 
FROM
    OrderDetails AS od
JOIN
    Products AS p ON od.[Product_ID] = p.[Product_ID]
GROUP BY
    p.[Product_Name]
ORDER BY
    Total_Units_Sold DESC;

-- 3 Bottom 10 Worst-Selling Products
SELECT TOP 10
    p.[Product_Name],
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Products AS p ON od.[Product_ID] = p.[Product_ID]
GROUP BY
    p.[Product_Name]
ORDER BY
    Total_Sales ASC;

-- 4 Sales by Product Category
SELECT
    p.Category,
    SUM(od.Sales) AS Total_Sales
FROM
    OrderDetails AS od
JOIN
    Products AS p ON od.[Product_ID] = p.[Product_ID]
GROUP BY
    p.Category
ORDER BY
    Total_Sales DESC;



--5 Order ANALYSIS


-- 1 Average Order Value (AOV) per Month Trend

SELECT
    FORMAT(o.[Order_Date], 'yyyy-MM') AS Month,
    SUM(od.Sales) / COUNT(DISTINCT o.[Order_ID]) AS AOV
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
GROUP BY
    FORMAT(o.[Order_Date], 'yyyy-MM')
ORDER BY
    Month;

-- 2 Busiest Month for Orders (Top 3)
SELECT TOP 3
    FORMAT([Order_Date], 'yyyy-MM') AS Month,
    COUNT(DISTINCT [Order_ID]) AS Total_Orders
FROM
    Orders 
GROUP BY
    FORMAT([Order_Date], 'yyyy-MM')
ORDER BY
    Total_Orders DESC;

-- 3 Orders by Day of Week
SELECT
    DATENAME(WEEKDAY, [Order_Date]) AS Day_Of_Week,
    COUNT(DISTINCT [Order_ID]) AS Total_Orders
FROM
    Orders 
GROUP BY
    DATENAME(WEEKDAY, [Order_Date])
ORDER BY
    Total_Orders DESC;

-- 4 Average Products per Order (UPO) Trend
SELECT
    FORMAT(o.[Order_Date], 'yyyy-MM') AS Month,CAST(COUNT(od.[Product_ID]) AS FLOAT) / COUNT(DISTINCT o.[Order_ID]) AS UPO_Trend
FROM
    OrderDetails AS od
JOIN
    Orders AS o ON od.[Order_ID] = o.[Order_ID]
GROUP BY
    FORMAT(o.[Order_Date], 'yyyy-MM')
ORDER BY
    Month;

-- 5 Ship Mode Percentage
WITH ShipModeCounts AS (
    SELECT
        [Ship_Mode],
        COUNT(DISTINCT [Order_ID]) AS Total_Orders
    FROM Orders
    GROUP BY [Ship_Mode]
)
SELECT
    [Ship_Mode],
    Total_Orders,
    (Total_Orders * 100.0 / SUM(Total_Orders) OVER()) AS Percentage
FROM ShipModeCounts
ORDER BY Percentage DESC;







python
cleaning: import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
sns.set(style="whitegrid", palette="muted", font_scale=1.1)

orders = pd.read_csv("Orders_clean.csv")
order_details = pd.read_csv("Order_Details_clean.csv")
customers = pd.read_csv("Customers_clean.csv")
products = pd.read_csv("Products_clean.csv")


print(orders.isnull().sum())

print(orders.duplicated().sum())

orders = orders.drop_duplicates()

print(orders.dtypes)

orders['Order_Date'] = pd.to_datetime(orders['Order_Date'], dayfirst=True)
orders['Ship_Date'] = pd.to_datetime(orders['Ship_Date'], dayfirst=True)


print(orders.dtypes)


print(orders.head())

orders.to_csv("Orders_clean.csv", index=False)

print(order_details.head())

print(order_details.isnull().sum())

print(order_details.duplicated().sum())

order_details = order_details.drop_duplicates()

order_details.to_csv("Order_Details.csv", index=False)

print(customers.head())
print(customers.isnull().sum())

print( customers.duplicated().sum())

customers = customers.drop_duplicates()

print(customers.dtypes)
customers.to_csv("Customers_clean.csv", index=False)

print(products.head())

print(products.isnull().sum())

print(products.duplicated().sum())

products = products.drop_duplicates()

print(products.dtypes)

products.to_csv("Products_cleanl.csv", index=False)

df_merged = pd.merge(order_details, products, on='Product ID')

df_merged = pd.merge(df_merged, orders, on='Order ID')

df = pd.merge(df_merged, customers, on='Customer ID')


#1 EXECUTIVE OVERVIEW (KPI Dashboard)
# 1. Total Sales
total_sales = df['Sales'].sum()
print(f"1. (Total Sales): {total_sales:,.2f} $")


# 2. Unique Orders

unique_orders = df['Order ID'].nunique() #
print(f"2. (Unique Orders): {unique_orders}")

# 3. Unique Customers

unique_customers = df['Customer ID'].nunique()
print(f"3. (Unique Customers): {unique_customers}")


# 4. Average Order Value (AOV)
aov = total_sales / unique_orders
print(f"4.  (AOV): {aov:,.2f} $")


# 5. Average Items per Order (UPO)

total_items_sold = len(df) 
upo = total_items_sold / unique_orders
print(f"5. (UPO): {upo:,.2f}")


df['Order Date'] = pd.to_datetime(df['Order Date'], dayfirst=True)

# 6. Sales Trend Over Time

print("\n6. (Sales Trend):")
df['YearMonth'] = df['Order Date'].dt.to_period('M')
monthly_sales = df.groupby('YearMonth')['Sales'].sum()
print(monthly_sales.tail(12)) 
print("\n" + "="*40 + "\n")


# 2 CUSTOMER ANALYSIS

# 1 Top 10 Customers by Sales

top_10_customers_sales = df.groupby('Customer Name')['Sales'].sum().nlargest(10)
print(top_10_customers_sales)

# 2 Top 10 Customers by Order Count

top_10_customers_orders = df.groupby('Customer Name')['Order ID'].nunique().nlargest(10)
print(top_10_customers_orders)


# 3 Customer Acquisition Trend

first_orders = df.groupby('Customer ID')['Order Date'].min()

new_customers_trend = first_orders.dt.to_period('M').value_counts().sort_index()
print(new_customers_trend.tail(12)) 


# 4 Distribution by Segment

segment_distribution = df.groupby('Segment')['Customer ID'].nunique().sort_values(ascending=False)
print(segment_distribution)


# 3 LOCATION ANALYSIS

# 1. Top 10 Cities by Sales

top_10_cities = df.groupby('City')['Sales'].sum().nlargest(10)
print(top_10_cities)


# 2. Bottom 10 Cities by Sales

bottom_10_cities = df.groupby('City')['Sales'].sum().nsmallest(10)
print(bottom_10_cities)


# 3. Sales by State

sales_by_state = df.groupby('State')['Sales'].sum().sort_values(ascending=False)
print(sales_by_state.head(10)) 


# 4. Sales by Region

sales_by_region = df.groupby('Region')['Sales'].sum().sort_values(ascending=False)
print(sales_by_region)


# 4 PRODUCT ANALYSIS

# 1. Top 10 Products by Sales

top_10_products_sales = df.groupby('Product Name')['Sales'].sum().nlargest(10)
print(top_10_products_sales)


# 2. Bottom 10 Products by Sales

bottom_10_products_sales = df.groupby('Product Name')['Sales'].sum().nsmallest(10)
print(bottom_10_products_sales)


# 3. Top Products by Popularity (Count)

top_10_products_count = df['Product Name'].value_counts().nlargest(10)
print(top_10_products_count)


# 4. Sales by Category

sales_by_category = df.groupby('Category')['Sales'].sum().sort_values(ascending=False)
print(sales_by_category)

# 5 ORDER ANALYSIS

# 1. Busiest Month for Orders

busiest_months = df.groupby('YearMonth')['Order ID'].nunique().sort_values(ascending=False)
print(busiest_months.head(5))


# 2. Busiest Day of Week

df['Day of Week'] = df['Order Date'].dt.day_name()
busiest_days = df['Day of Week'].value_counts()
print(busiest_days)


# 3. AOV Trend Over Time

monthly_analysis = df.groupby('YearMonth').agg(
    Monthly_Sales=('Sales', 'sum'),
    Unique_Orders=('Order ID', 'nunique')
)
monthly_analysis['AOV_Trend'] = monthly_analysis['Monthly_Sales'] / monthly_analysis['Unique_Orders']
print(monthly_analysis['AOV_Trend'].tail(12))


# 4. Units per Order (UPO) Trend Over Time

monthly_analysis_items = df.groupby('YearMonth').agg(
    Total_Items=('Order ID', 'count'), # بنعد كل السطور
    Unique_Orders=('Order ID', 'nunique')
)
monthly_analysis_items['UPO_Trend'] = monthly_analysis_items['Total_Items'] / monthly_analysis_items['Unique_Orders']
print(monthly_analysis_items['UPO_Trend'].tail(12))



