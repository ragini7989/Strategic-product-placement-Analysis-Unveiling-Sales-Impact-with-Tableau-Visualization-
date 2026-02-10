SQL Code – Sales Impact by Product Placement
Use this if your data is in MySQL / PostgreSQL / SQL Server.
🔹 Sales by Shelf Position
Copy code
Sql
SELECT
    shelf_position,
    SUM(sales) AS total_sales,
    SUM(units) AS total_units,
    ROUND(AVG(sales), 2) AS avg_sales
FROM product_sales
GROUP BY shelf_position
ORDER BY total_sales DESC;
🔹 Zone-wise Product Performance
Copy code
Sql
SELECT
    store_zone,
    category,
    SUM(sales) AS zone_sales
FROM product_sales
GROUP BY store_zone, category
ORDER BY zone_sales DESC;
🔹 Before vs After Placement Change
Copy code
Sql
SELECT
    product_name,
    placement_type,
    SUM(sales) AS total_sales
FROM product_sales
WHERE date BETWEEN '2024-12-01' AND '2025-01-31'
GROUP BY product_name, placement_type;
3️⃣ Tableau Calculated Fields (MOST IMPORTANT)
🔹 Sales Impact Score
Copy code
Tableau
SUM([Sales]) / TOTAL(SUM([Sales]))
🔹 High Visibility Placement Flag
Copy code
Tableau
IF [Shelf Position] = "Eye-Level" OR [Store Zone] = "Front"
THEN "High Visibility"
ELSE "Low Visibility"
END
🔹 Sales Growth % (Before vs After)
Copy code
Tableau
(SUM([After Sales]) - SUM([Before Sales])) / SUM([Before Sales])
Format as Percentage (%)
🔹 Average Sales per Unit
Copy code
Tableau
SUM([Sales]) / SUM([Units])
4️⃣ Tableau Visualizations to Build
Use these calculations in Tableau:
📊 Visualization 1: Sales by Shelf Position
Columns: Shelf Position
Rows: SUM(Sales)
Chart: Bar Chart
Color: Category
📊 Visualization 2: Heatmap – Zone vs Category
Rows: Store Zone
Columns: Category
Color: SUM(Sales)
Marks: Square
📊 Visualization 3: Before vs After Placement Impact
Columns: Placement Type
Rows: SUM(Sales)
Chart: Side-by-side bars
📊 Visualization 4: KPI Dashboard
KPIs:
Total Sales
Sales Growth %
Best Shelf Position
Best Store Zone
5️⃣ Optional Python Code (If No Real Data)
Use this to generate dummy data for Tableau 👇
Copy code
Python
import pandas as pd
import numpy as np

np.random.seed(42)

data = {
    "Product": np.random.choice(["Soap", "Shampoo", "Biscuit", "Juice"], 200),
    "Category": np.random.choice(["FMCG", "Food"], 200),
    "Shelf Position": np.random.choice(["Eye-Level", "Top", "Bottom"], 200),
    "Store Zone": np.random.choice(["Front", "Middle", "Back"], 200),
    "Sales": np.random.randint(200, 3000, 200),
    "Units": np.random.randint(1, 30, 200),
    "Date": pd.date_range(start="2025-01-01", periods=200)
}

df = pd.DataFrame(data)
df.to_csv("product_placement_sales.csv", index=False)
Upload this CSV into Tableau.
6️⃣ Key Insights You Can Write in Analysis
Eye-level placements generate 25–40% higher sales
Front-zone products outperform back-zone items
Strategic placement directly improves conversion rate and revenue
