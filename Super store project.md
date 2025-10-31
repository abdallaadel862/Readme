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

