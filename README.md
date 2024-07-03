# Order-Analysis

This project involves analyzing an orders dataset, performing data cleaning and transformation, and loading the data into a MySQL database for further analysis using SQL queries.

## Table of Contents
- [Dataset](#dataset)
- [Data Cleaning](#data-cleaning)
- [Data Transformation](#data-transformation)
- [Loading Data into MySQL](#loading-data-into-mysql)
- [SQL Queries](#sql-queries)
- [Conclusion](#conclusion)
- [Dependencies](#dependencies)
- [Usage](#usage)
- [Author](#author)
- [License](#license)

## Dataset
The dataset used in this project is a CSV file containing information about various orders. The dataset includes columns such as:
- `Order ID`: Unique identifier for each order
- `Ship Mode`: Mode of shipment
- `Order Date`: Date of the order
- `List Price`: List price of the product
- `Cost Price`: Cost price of the product
- `Discount Percent`: Discount percentage applied
- `Region`: Region of the order
- `Product ID`: Unique identifier for each product

## Data Cleaning
1. **Missing Values:**
   - Loaded the dataset and identified missing values using `na_values`.

2. **Column Renaming:**
   - Renamed columns to lowercase and replaced spaces with underscores for consistency.

3. **Handling Discounts and Prices:**
   - Created new columns for `discount`, `sale_price`, and `profit` based on existing columns.
   - Converted `order_date` to a datetime format.

4. **Dropping Unnecessary Columns:**
   - Dropped columns that are no longer needed: `list_price`, `cost_price`, and `discount_percent`.

## Data Transformation
1. **Creating New Columns:**
   - Calculated discount, sale price, and profit for each order.

2. **Date Conversion:**
   - Converted `order_date` to a datetime object for easier manipulation.

## Loading Data into MySQL
1. **Setting Up MySQL Connection:**
   - Used SQLAlchemy to create an engine for MySQL connection.
   - Established connection parameters such as username, password, server, port, and database name.

2. **Loading Data:**
   - Loaded the cleaned DataFrame into MySQL using the `to_sql` method with `if_exists='append'`.

## SQL Queries
1. **Top 10 Highest Revenue Generating Products:**
   ```sql
   SELECT product_id, SUM(sale_price) AS sales
   FROM df_orders
   GROUP BY product_id
   ORDER BY sales DESC
   LIMIT 10;
   ```

2. **Top 5 Highest Selling Products in Each Region:**
   ```sql
   WITH cte AS (
       SELECT region, product_id, SUM(sale_price) AS sales
       FROM df_orders
       GROUP BY region, product_id
   )
   SELECT *
   FROM (
       SELECT *,
              ROW_NUMBER() OVER (PARTITION BY region ORDER BY sales DESC) AS rn
       FROM cte
   ) AS subquery
   WHERE rn <= 5;
   ```

3. **Month Over Month Growth Comparison for 2022 and 2023 Sales:**
   ```sql
   WITH cte AS (
       SELECT YEAR(order_date) AS order_year,
              MONTH(order_date) AS order_month,
              SUM(sale_price) AS sales
       FROM df_orders
       GROUP BY YEAR(order_date), MONTH(order_date)
   )
   SELECT order_month,
          SUM(CASE WHEN order_year = 2022 THEN sales ELSE 0 END) AS sales_2022,
          SUM(CASE WHEN order_year = 2023 THEN sales ELSE 0 END) AS sales_2023
   FROM cte
   GROUP BY order_month
   ORDER BY order_month;
   ```

## Conclusion
This project provides a comprehensive analysis of the orders dataset, from data cleaning and transformation to loading the data into a MySQL database and performing complex SQL queries. The insights gained from the analysis can help in understanding sales trends, top-performing products, and regional sales performance.

## Dependencies
- pandas
- numpy
- matplotlib
- seaborn
- sqlalchemy
- pymysql

## Usage
1. Ensure you have the necessary libraries installed:
   ```bash
   pip install pandas numpy matplotlib seaborn sqlalchemy pymysql
   ```
2. Load the dataset and run the analysis using the provided code snippets.
3. Execute the SQL queries in a MySQL environment to gain insights from the data.

## Author
[Your Name]

## License
This project is licensed under the MIT License - see the LICENSE.md file for details.
