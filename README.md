# Strategic Product Placement Analysis: Unveiling Sales Impact with Tableau Visualization

import pandas as pd
import numpy as np
import sqlite3
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import r2_score, mean_squared_error
import datetime
import random

# ----------------------------
# 1. Generate Synthetic Dataset
# ----------------------------

np.random.seed(42)

num_records = 1000

products = ["Chocolate", "Biscuits", "Chips", "Juice", "Soft Drink", "Soap", "Shampoo"]
categories = ["Snacks", "Snacks", "Snacks", "Beverages", "Beverages", "Personal Care", "Personal Care"]
store_locations = ["Urban", "Suburban", "Rural"]
shelf_positions = ["Eye Level", "Top Shelf", "Bottom Shelf"]

data = []

for i in range(num_records):
    product_index = random.randint(0, len(products)-1)
    shelf = random.choice(shelf_positions)

    # Shelf impact multiplier
    if shelf == "Eye Level":
        multiplier = 1.3
    elif shelf == "Top Shelf":
        multiplier = 1.0
    else:
        multiplier = 0.8

    base_sales = np.random.randint(100, 500)
    sales_amount = base_sales * multiplier
    quantity = np.random.randint(1, 20)

    data.append([
        i+1,
        products[product_index],
        categories[product_index],
        random.choice(store_locations),
        shelf,
        round(sales_amount, 2),
        quantity,
        datetime.date(2025, random.randint(1,12), random.randint(1,28))
    ])

columns = ["order_id", "product_name", "category", "store_location",
           "shelf_position", "sales_amount", "quantity", "order_date"]

df = pd.DataFrame(data, columns=columns)

# Save dataset for Tableau
df.to_csv("strategic_product_placement_sales.csv", index=False)

# ----------------------------
# 2. SQL Analysis using SQLite
# ----------------------------

conn = sqlite3.connect("sales_analysis.db")
df.to_sql("sales_data", conn, if_exists="replace", index=False)

query_shelf = """
SELECT shelf_position,
       SUM(sales_amount) AS total_sales,
       SUM(quantity) AS total_quantity
FROM sales_data
GROUP BY shelf_position
ORDER BY total_sales DESC;
"""

query_location = """
SELECT store_location,
       SUM(sales_amount) AS location_sales
FROM sales_data
GROUP BY store_location;
"""

shelf_results = pd.read_sql_query(query_shelf, conn)
location_results = pd.read_sql_query(query_location, conn)

print("\nSales by Shelf Position:")
print(shelf_results)

print("\nSales by Store Location:")
print(location_results)

# ----------------------------
# 3. Feature Engineering
# ----------------------------

df['month'] = pd.to_datetime(df['order_date']).dt.month
df['shelf_encoded'] = df['shelf_position'].astype('category').cat.codes
df['location_encoded'] = df['store_location'].astype('category').cat.codes

# ----------------------------
# 4. Machine Learning Model
# ----------------------------

X = df[['shelf_encoded', 'location_encoded', 'quantity']]
y = df['sales_amount']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

model = LinearRegression()
model.fit(X_train, y_train)

predictions = model.predict(X_test)

r2 = r2_score(y_test, predictions)
mse = mean_squared_error(y_test, predictions)

print("\nModel Performance:")
print("R2 Score:", round(r2, 4))
print("Mean Squared Error:", round(mse, 2))

print("\nShelf Impact Coefficients:")
for feature, coef in zip(X.columns, model.coef_):
    print(f"{feature}: {round(coef, 2)}")

# ----------------------------
# 5. KPI Calculations
# ----------------------------

total_sales = df['sales_amount'].sum()
avg_sales = df['sales_amount'].mean()
best_shelf = shelf_results.iloc[0]['shelf_position']

print("\nKey Insights:")
print("Total Sales:", round(total_sales, 2))
print("Average Sales per Order:", round(avg_sales, 2))
print("Best Performing Shelf Position:", best_shelf)

# ----------------------------
# 6. Export Processed Data for Tableau
# ----------------------------

df['sales_growth'] = df['sales_amount'].pct_change().fillna(0)

df.to_csv("tableau_ready_product_placement_data.csv", index=False)

conn.close()

print("\nAll files generated successfully. Ready for Tableau Visualization.")
