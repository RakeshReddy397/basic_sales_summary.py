# basic_sales_summary.py """
Basic Sales Summary using SQLite and Python
-------------------------------------------
This script demonstrates how to:
- Create a tiny in-memory SQLite database
- Insert sample sales data
- Write SQL queries to summarize total quantity and revenue
- Display results in the terminal
- Show a bar chart of revenue by product

Author: Kanna
"""

import sqlite3
import matplotlib.pyplot as plt

# Step 1: Connect to SQLite database (in-memory)
conn = sqlite3.connect(':memory:')  # Temporary database in RAM
cursor = conn.cursor()

# Step 2: Create the sales table
cursor.execute('''
CREATE TABLE sales (
    id INTEGER PRIMARY KEY,
    product TEXT,
    quantity INTEGER,
    price REAL
)
''')

# Step 3: Insert sample data into the table
sample_data = [
    ('Apple', 10, 0.50),
    ('Banana', 5, 0.30),
    ('Orange', 8, 0.40),
    ('Apple', 7, 0.50),
    ('Banana', 3, 0.30),
    ('Orange', 6, 0.40),
]

cursor.executemany('INSERT INTO sales (product, quantity, price) VALUES (?, ?, ?)', sample_data)
conn.commit()

# Step 4: Run SQL query to get total quantity and total revenue
query = '''
SELECT product,
       SUM(quantity) AS total_quantity,
       SUM(quantity * price) AS total_revenue
FROM sales
GROUP BY product
'''

cursor.execute(query)
results = cursor.fetchall()

# Step 5: Print results
print("Sales Summary:")
print("Product\tTotal Quantity\tTotal Revenue")
for row in results:
    print(f"{row[0]}\t{row[1]}\t\t${row[2]:.2f}")

# Step 6: Plot a bar chart of revenue by product
products = [row[0] for row in results]
revenues = [row[2] for row in results]

plt.bar(products, revenues, color='skyblue')
plt.title('Total Revenue by Product')
plt.xlabel('Product')
plt.ylabel('Revenue ($)')
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

# Step 7: Close the database connection
conn.close()



Sales Summary:
Product   Total Quantity   Total Revenue
Apple     17               $8.50
Banana    8                $2.40
Orange    14               $5.60

