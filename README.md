# Workflow---SQL
Data Analyst — Python + SQL Interview & Practical Handbook

A practical, interview-oriented reference for a Data Analyst with ~2 years of experience.

The repository follows the real analytical workflow:

**Load → Inspect → Clean → Explore → Analyze → Transform → Export → SQL → Business Problems**

* * *
Export CSV

    df.to_csv(
        "cleaned_data.csv",
        index=False
    )

## Export Excel

    df.to_excel(
        "cleaned_data.xlsx",
        index=False
    )

## Export to MySQL

    from sqlalchemy import create_engine
    
    engine = create_engine(
        "mysql+pymysql://username:password@localhost/database"
    )
    
    df.to_sql(
        "orders",
        engine,
        if_exists="append",
        index=False
    )

Options:

    if_exists="fail"
    if_exists="replace"
    if_exists="append"

# 1. Repository Structure

    Data_Analyst
    │
    ├── Python
    │   ├── 01_Imports_Libraries
    │   ├── 02_Data_Loading
    │   ├── 03_Data_Cleaning
    │   ├── 04_EDA
    │   ├── 05_SQL_Loading
    │   └── 06_Frequent_Scenarios
    │
    ├── SQL
    │   ├── 01_Basics
    │   ├── 02_Filtering
    │   ├── 03_Aggregation
    │   ├── 04_Joins
    │   ├── 05_Conditional_Logic
    │   ├── 06_Date_Time
    │   ├── 07_String_Operations
    │   ├── 08_Data_Cleaning
    │   ├── 09_Window_Functions
    │   ├── 10_CTE
    │   ├── 11_Subqueries
    │   ├── 12_Views
    │   ├── 13_Indexing
    │   ├── 14_Set_Operations
    │   ├── 15_Transactions
    │   ├── 16_DDL_DML_DCL_TCL
    │   └── 17_Interview_Frequent_Scenarios
    │
    └── Excel
        └── formulas.xlsx   # Excel content will be added later

* * *

# 2. Common Dataset

The examples below use a small e-commerce dataset so that concepts connect across Python and SQL.

## customers

| customer_id | customer_name | city | state | signup_date |
| --- | --- | --- | --- | --- |
| 101 | Amit | Pune | Maharashtra | 2024-01-15 |
| 102 | Priya | Mumbai | Maharashtra | 2024-02-10 |
| 103 | Rahul | Delhi | Delhi | 2024-02-18 |
| 104 | Neha | Pune | Maharashtra | 2024-03-01 |
| 105 | Arjun | Bengaluru | Karnataka | 2024-03-15 |

## products

| product_id | product_name | category | brand | cost | selling_price |
| --- | --- | --- | --- | --- | --- |
| 1   | Laptop | Electronics | Dell | 50000 | 65000 |
| 2   | Mouse | Electronics | Logitech | 500 | 800 |
| 3   | Chair | Furniture | IKEA | 4000 | 6500 |
| 4   | Desk | Furniture | IKEA | 7000 | 10000 |
| 5   | Shoes | Fashion | Nike | 2500 | 4000 |

## orders

| order_id | customer_id | product_id | order_date | quantity | unit_price | discount | payment_method |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1001 | 101 | 1   | 2024-04-01 | 1   | 65000 | 0.05 | Card |
| 1002 | 101 | 2   | 2024-04-05 | 2   | 800 | 0.00 | UPI |
| 1003 | 102 | 3   | 2024-04-07 | 1   | 6500 | 0.10 | Card |
| 1004 | 103 | 5   | 2024-04-12 | 2   | 4000 | 0.05 | UPI |
| 1005 | 104 | 4   | 2024-05-02 | 1   | 10000 | 0.00 | Cash |
| 1006 | 105 | 2   | 2024-05-08 | 3   | 800 | 0.00 | Card |
| 1007 | 101 | 5   | 2024-05-15 | 1   | 4000 | 0.10 | UPI |
| 1008 | 102 | 1   | 2024-06-01 | 1   | 65000 | 0.00 | Card |


9. SQL

The SQL examples use MySQL-style syntax where database-specific functions are required.

* * *

# 9.1 01_Basics

## SELECT

    SELECT *
    FROM customers;

Select columns:

    SELECT
        customer_id,
        customer_name,
        city
    FROM customers;

## DISTINCT

    SELECT DISTINCT city
    FROM customers;

## WHERE

    SELECT *
    FROM orders
    WHERE quantity > 2;

## ORDER BY

    SELECT *
    FROM orders
    ORDER BY unit_price DESC;

## LIMIT

    SELECT *
    FROM orders
    ORDER BY unit_price DESC
    LIMIT 5;

## Alias

    SELECT
        customer_name AS Customer,
        city AS Location
    FROM customers;

* * *

# 9.2 02_Filtering

## AND

    SELECT *
    FROM orders
    WHERE quantity > 1
      AND payment_method = 'Card';

## OR

    SELECT *
    FROM customers
    WHERE city = 'Pune'
       OR city = 'Mumbai';

## IN

    SELECT *
    FROM customers
    WHERE city IN ('Pune', 'Mumbai', 'Delhi');

## BETWEEN

    SELECT *
    FROM orders
    WHERE unit_price BETWEEN 1000 AND 10000;

## LIKE

    SELECT *
    FROM customers
    WHERE customer_name LIKE 'A%';

## EXISTS

    SELECT *
    FROM customers c
    WHERE EXISTS (
        SELECT 1
        FROM orders o
        WHERE o.customer_id = c.customer_id
    );

## NULL

    SELECT *
    FROM customers
    WHERE city IS NULL;

* * *

# 9.3 03_Aggregation

    SELECT
        COUNT(*) AS total_orders,
        SUM(quantity) AS total_quantity,
        AVG(unit_price) AS avg_price,
        MIN(unit_price) AS min_price,
        MAX(unit_price) AS max_price
    FROM orders;

## GROUP BY

    SELECT
        product_id,
        SUM(quantity) AS total_quantity
    FROM orders
    GROUP BY product_id;

## HAVING

    SELECT
        product_id,
        SUM(quantity) AS total_quantity
    FROM orders
    GROUP BY product_id
    HAVING SUM(quantity) > 2;

* * *

# 9.4 04_Joins

## INNER JOIN

    SELECT
        o.order_id,
        c.customer_name,
        o.order_date
    FROM orders o
    INNER JOIN customers c
        ON o.customer_id = c.customer_id;

## LEFT JOIN

    SELECT
        c.customer_id,
        c.customer_name,
        o.order_id
    FROM customers c
    LEFT JOIN orders o
        ON c.customer_id = o.customer_id;

Find customers without orders:

    SELECT
        c.customer_id,
        c.customer_name
    FROM customers c
    LEFT JOIN orders o
        ON c.customer_id = o.customer_id
    WHERE o.order_id IS NULL;

## Multiple JOINs

    SELECT
        o.order_id,
        c.customer_name,
        p.product_name,
        p.category,
        o.quantity
    FROM orders o
    JOIN customers c
        ON o.customer_id = c.customer_id
    JOIN products p
        ON o.product_id = p.product_id;

* * *

# 9.5 05_Conditional_Logic

## CASE

    SELECT
        order_id,
        quantity,
        CASE
            WHEN quantity >= 3 THEN 'High'
            WHEN quantity >= 2 THEN 'Medium'
            ELSE 'Low'
        END AS order_size
    FROM orders;

## CASE with aggregation

    SELECT
        SUM(
            CASE
                WHEN payment_method = 'Card'
                THEN quantity * unit_price
                ELSE 0
            END
        ) AS card_sales
    FROM orders;

## Conditional aggregation

    SELECT
        SUM(
            CASE
                WHEN payment_method = 'UPI'
                THEN 1
                ELSE 0
            END
        ) AS upi_orders,
    
        SUM(
            CASE
                WHEN payment_method = 'Card'
                THEN 1
                ELSE 0
            END
        ) AS card_orders
    
    FROM orders;

* * *

# 9.6 06_Date_Time

## Current date

    SELECT CURRENT_DATE();

## Current date/time

    SELECT NOW();

## Year

    SELECT
        YEAR(order_date) AS order_year
    FROM orders;

## Month number

    SELECT
        MONTH(order_date) AS order_month
    FROM orders;

## Month name

    SELECT
        MONTHNAME(order_date) AS month_name
    FROM orders;

## Quarter

    SELECT
        QUARTER(order_date) AS quarter
    FROM orders;

## Day

    SELECT
        DAY(order_date) AS day_number
    FROM orders;

## Day of week

    SELECT
        DAYOFWEEK(order_date) AS day_of_week
    FROM orders;

## Day name

    SELECT
        DAYNAME(order_date) AS day_name
    FROM orders;

## Week

    SELECT
        WEEK(order_date) AS week_number
    FROM orders;

## Date difference

    SELECT
        DATEDIFF(
            '2024-06-30',
            order_date
        ) AS days_since_order
    FROM orders;

## Add date

    SELECT
        DATE_ADD(
            order_date,
            INTERVAL 7 DAY
        ) AS expected_date
    FROM orders;

## Monthly sales

    SELECT
        YEAR(order_date) AS year,
        MONTH(order_date) AS month,
        SUM(quantity * unit_price) AS sales
    FROM orders
    GROUP BY
        YEAR(order_date),
        MONTH(order_date)
    ORDER BY year, month;

* * *

# 9.7 07_String_Operations

    SELECT
        UPPER(customer_name) AS upper_name,
        LOWER(customer_name) AS lower_name,
        TRIM(customer_name) AS clean_name
    FROM customers;

Concatenate:

    SELECT
        CONCAT(customer_name, ' - ', city) AS customer_location
    FROM customers;

Substring:

    SELECT
        LEFT(customer_name, 3) AS first_three
    FROM customers;

Replace:

    SELECT
        REPLACE(city, 'Mumbai', 'Bombay') AS city
    FROM customers;

Length:

    SELECT
        customer_name,
        LENGTH(customer_name) AS name_length
    FROM customers;

* * *

# 9.8 08_Data_Cleaning

## COALESCE

    SELECT
        customer_id,
        COALESCE(city, 'Unknown') AS city
    FROM customers;

## NULLIF

    SELECT
        NULLIF(quantity, 0)
    FROM orders;

## Detect duplicates

    SELECT
        customer_id,
        customer_name,
        COUNT(*) AS record_count
    FROM customers
    GROUP BY
        customer_id,
        customer_name
    HAVING COUNT(*) > 1;

## Standardize text

    SELECT
        UPPER(TRIM(city)) AS clean_city
    FROM customers;

## Replace values

    UPDATE customers
    SET city = 'Mumbai'
    WHERE city = 'Bombay';

## Data type conversion

MySQL:

    SELECT
        CAST(unit_price AS DECIMAL(12,2))
    FROM orders;

* * *

# 9.9 09_Window_Functions

## ROW_NUMBER

    SELECT
        employee_id,
        employee_name,
        department,
        salary,
        ROW_NUMBER() OVER (
            PARTITION BY department
            ORDER BY salary DESC
        ) AS row_num
    FROM employees;

## RANK

    SELECT
        employee_name,
        salary,
        RANK() OVER (
            ORDER BY salary DESC
        ) AS salary_rank
    FROM employees;

## DENSE_RANK

    SELECT
        employee_name,
        salary,
        DENSE_RANK() OVER (
            ORDER BY salary DESC
        ) AS salary_rank
    FROM employees;

## LAG

    SELECT
        order_date,
        sales,
        LAG(sales) OVER (
            ORDER BY order_date
        ) AS previous_sales
    FROM daily_sales;

## LEAD

    SELECT
        order_date,
        sales,
        LEAD(sales) OVER (
            ORDER BY order_date
        ) AS next_sales
    FROM daily_sales;

## Running total

    SELECT
        order_date,
        sales,
        SUM(sales) OVER (
            ORDER BY order_date
        ) AS running_sales
    FROM daily_sales;

## Moving average

    SELECT
        order_date,
        sales,
        AVG(sales) OVER (
            ORDER BY order_date
            ROWS BETWEEN 6 PRECEDING
            AND CURRENT ROW
        ) AS seven_day_average
    FROM daily_sales;

* * *

# 9.10 10_CTE

## Basic CTE

    WITH high_value_orders AS (
        SELECT *
        FROM orders
        WHERE unit_price > 10000
    )
    SELECT *
    FROM high_value_orders;

## CTE + GROUP BY

    WITH category_sales AS (
        SELECT
            p.category,
            SUM(o.quantity * o.unit_price) AS sales
        FROM orders o
        JOIN products p
            ON o.product_id = p.product_id
        GROUP BY p.category
    )
    SELECT *
    FROM category_sales
    WHERE sales > 10000;

## Multiple CTEs

    WITH customer_sales AS (
        SELECT
            customer_id,
            SUM(quantity * unit_price) AS sales
        FROM orders
        GROUP BY customer_id
    ),
    
    customer_orders AS (
        SELECT
            customer_id,
            COUNT(*) AS order_count
        FROM orders
        GROUP BY customer_id
    )
    
    SELECT
        cs.customer_id,
        cs.sales,
        co.order_count
    FROM customer_sales cs
    JOIN customer_orders co
        ON cs.customer_id = co.customer_id;

## CTE + Window Function

    WITH ranked_products AS (
        SELECT
            product_id,
            SUM(quantity * unit_price) AS sales,
            RANK() OVER (
                ORDER BY SUM(quantity * unit_price) DESC
            ) AS sales_rank
        FROM orders
        GROUP BY product_id
    )
    SELECT *
    FROM ranked_products
    WHERE sales_rank <= 3;

* * *

# 9.11 11_Subqueries

## Scalar subquery

    SELECT
        employee_name,
        salary
    FROM employees
    WHERE salary > (
        SELECT AVG(salary)
        FROM employees
    );

## Correlated subquery

    SELECT
        e.employee_name,
        e.department,
        e.salary
    FROM employees e
    WHERE e.salary > (
        SELECT AVG(e2.salary)
        FROM employees e2
        WHERE e2.department = e.department
    );

* * *

# 9.12 12_Views

## Create view

    CREATE VIEW customer_sales AS
    SELECT
        c.customer_id,
        c.customer_name,
        SUM(o.quantity * o.unit_price) AS total_sales
    FROM customers c
    JOIN orders o
        ON c.customer_id = o.customer_id
    GROUP BY
        c.customer_id,
        c.customer_name;

Use:

    SELECT *
    FROM customer_sales;

Drop:

    DROP VIEW customer_sales;

### Why use a view?

A view can provide a reusable query layer for reporting, simplify complex queries, and restrict users to selected columns/rows.

* * *

# 9.13 13_Indexing

## Basic index

    CREATE INDEX idx_customer_id
    ON orders(customer_id);

## Composite index

    CREATE INDEX idx_customer_date
    ON orders(customer_id, order_date);

## Unique index

    CREATE UNIQUE INDEX idx_customer_email
    ON customers(email);

## Drop index — MySQL

    DROP INDEX idx_customer_id
    ON orders;

### Interview concepts

An index can speed up searches, joins, and sorting when the database can use it effectively.

Trade-offs:

* Additional storage
* INSERT/UPDATE/DELETE overhead
* Too many indexes can hurt write performance
* Low-selectivity columns may provide limited benefit depending on workload
* Composite index column order matters

* * *

# 9.14 14_Set_Operations

## UNION

    SELECT city
    FROM customers
    
    UNION
    
    SELECT city
    FROM suppliers;

## UNION ALL

    SELECT city
    FROM customers
    
    UNION ALL
    
    SELECT city
    FROM suppliers;

## INTERSECT

    SELECT customer_id
    FROM current_customers
    
    INTERSECT
    
    SELECT customer_id
    FROM previous_customers;

## EXCEPT

    SELECT customer_id
    FROM customers
    
    EXCEPT
    
    SELECT customer_id
    FROM orders;

Note: set-operation support varies by database engine.

* * *

# 9.15 15_Transactions

    START TRANSACTION;
    
    UPDATE accounts
    SET balance = balance - 1000
    WHERE account_id = 101;
    
    UPDATE accounts
    SET balance = balance + 1000
    WHERE account_id = 102;
    
    COMMIT;

Rollback:

    START TRANSACTION;
    
    UPDATE accounts
    SET balance = balance - 1000
    WHERE account_id = 101;
    
    ROLLBACK;

* * *

# 9.16 16_DDL_DML_DCL_TCL

## DDL

    CREATE TABLE test (
        id INT,
        name VARCHAR(100)
    );

    ALTER TABLE test
    ADD COLUMN age INT;

    DROP TABLE test;

    TRUNCATE TABLE test;

## DML

    INSERT INTO customers
    (customer_id, customer_name, city)
    VALUES
    (106, 'Kiran', 'Pune');

    UPDATE customers
    SET city = 'Mumbai'
    WHERE customer_id = 106;

    DELETE FROM customers
    WHERE customer_id = 106;

## TCL

    COMMIT;

    ROLLBACK;

## DCL

    GRANT SELECT
    ON database_name.customers
    TO analyst_user;

    REVOKE SELECT
    ON database_name.customers
    FROM analyst_user;

* * *

# 10. SQL — 17_Interview_Frequent_Scenarios

These are the problems that should become automatic through practice.

* * *

## Second Highest Salary

    SELECT MAX(salary) AS second_highest
    FROM employees
    WHERE salary < (
        SELECT MAX(salary)
        FROM employees
    );

* * *

## Nth Highest Salary

    WITH ranked AS (
        SELECT
            employee_name,
            salary,
            DENSE_RANK() OVER (
                ORDER BY salary DESC
            ) AS salary_rank
        FROM employees
    )
    SELECT *
    FROM ranked
    WHERE salary_rank = 3;

* * *

## Top N Per Group

    WITH ranked AS (
        SELECT
            department,
            employee_name,
            salary,
            ROW_NUMBER() OVER (
                PARTITION BY department
                ORDER BY salary DESC
            ) AS rn
        FROM employees
    )
    SELECT *
    FROM ranked
    WHERE rn <= 3;

* * *

## Latest Record Per Customer

    WITH ranked AS (
        SELECT
            o.*,
            ROW_NUMBER() OVER (
                PARTITION BY customer_id
                ORDER BY order_date DESC
            ) AS rn
        FROM orders o
    )
    SELECT *
    FROM ranked
    WHERE rn = 1;

* * *

## Customers Without Orders

    SELECT
        c.customer_id,
        c.customer_name
    FROM customers c
    LEFT JOIN orders o
        ON c.customer_id = o.customer_id
    WHERE o.order_id IS NULL;

* * *

## Sales by Category

    SELECT
        p.category,
        SUM(o.quantity * o.unit_price) AS total_sales
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY p.category
    ORDER BY total_sales DESC;

* * *

## Month-over-Month Growth

    WITH monthly_sales AS (
        SELECT
            DATE_FORMAT(order_date, '%Y-%m') AS month,
            SUM(quantity * unit_price) AS sales
        FROM orders
        GROUP BY DATE_FORMAT(order_date, '%Y-%m')
    ),
    
    sales_with_previous AS (
        SELECT
            month,
            sales,
            LAG(sales) OVER (
                ORDER BY month
            ) AS previous_sales
        FROM monthly_sales
    )
    
    SELECT
        month,
        sales,
        previous_sales,
        ROUND(
            (sales - previous_sales)
            / NULLIF(previous_sales, 0) * 100,
            2
        ) AS growth_percentage
    FROM sales_with_previous;

* * *

# 11. Interview Thinking Framework

When given a Data Analyst problem, think in this order:

    1. What is the business question?
                 ↓
    2. What is the grain of the data?
                 ↓
    3. Which tables/columns are required?
                 ↓
    4. Is the data clean?
                 ↓
    5. Do I need filtering?
                 ↓
    6. Do I need JOIN?
                 ↓
    7. Do I need GROUP BY?
                 ↓
    8. Do I need CASE?
                 ↓
    9. Do I need a Window Function?
                 ↓
    10. Would a CTE make the logic clearer?
                 ↓
    11. How should the result be validated?

* * *

# 12. Python Interview Thinking Framework

    Load
     ↓
    Inspect
     ↓
    Check shape/types/nulls/duplicates
     ↓
    Clean
     ↓
    Transform
     ↓
    Validate
     ↓
    EDA
     ↓
    Visualize
     ↓
    Business Insight
     ↓
    Export / SQL / Power BI

* * *

# 13. Python vs SQL — Concept Mapping

| Task | Python/Pandas | SQL |
| --- | --- | --- |
| Filter | `df[df["Sales"] > 100]` | `WHERE Sales > 100` |
| Sort | `sort_values()` | `ORDER BY` |
| Group | `groupby()` | `GROUP BY` |
| Aggregate | `.sum()`, `.mean()` | `SUM()`, `AVG()` |
| Conditional | `np.where()` | `CASE` |
| Join | `pd.merge()` | `JOIN` |
| Missing values | `fillna()` | `COALESCE()` |
| Duplicate detection | `duplicated()` | `GROUP BY ... HAVING` |
| Ranking | `.rank()` | `RANK()` |
| Row numbering | `cumcount()` / rank | `ROW_NUMBER()` |
| Previous row | `shift()` | `LAG()` |
| Next row | `shift(-1)` | `LEAD()` |
| Running total | `cumsum()` | `SUM() OVER()` |
| Date year | `.dt.year` | `YEAR()` |
| Date month | `.dt.month` | `MONTH()` |
| Date name | `.dt.day_name()` | `DAYNAME()` |
| Pivot | `pivot_table()` | conditional aggregation / PIVOT where supported |
| Concatenate | `pd.concat()` | `UNION ALL` |
| CTE | N/A directly | `WITH` |
| View | N/A directly | `CREATE VIEW` |
| Index | DataFrame index ≠ DB index | `CREATE INDEX` |

* * *

# 14. Most Important Data Cleaning Checklist

Before EDA, check:

    [ ] Correct number of rows and columns
    [ ] Correct column names
    [ ] Correct data types
    [ ] Missing values
    [ ] Duplicate rows
    [ ] Duplicate business keys
    [ ] Inconsistent text
    [ ] Extra spaces
    [ ] Inconsistent categories
    [ ] Invalid dates
    [ ] Incorrect numeric values
    [ ] Currency symbols
    [ ] Numbers stored as text
    [ ] Outliers
    [ ] Impossible values
    [ ] Negative values where inappropriate
    [ ] Date ranges
    [ ] Category values
    [ ] Referential consistency

* * *

# 15. EDA Checklist

## Numerical

    [ ] Count
    [ ] Mean
    [ ] Median
    [ ] Minimum
    [ ] Maximum
    [ ] Standard deviation
    [ ] Quartiles
    [ ] Distribution
    [ ] Outliers

## Categorical

    [ ] Unique values
    [ ] Frequency
    [ ] Percentage share
    [ ] Rare categories
    [ ] Inconsistent categories

## Relationships

    [ ] Correlation
    [ ] Trend
    [ ] Category comparison
    [ ] Time comparison
    [ ] Distribution comparison
    [ ] Segment comparison

* * *

# 16. Common Interview Questions

## SQL

1. WHERE vs HAVING
2. INNER JOIN vs LEFT JOIN
3. UNION vs UNION ALL
4. IN vs EXISTS
5. DELETE vs TRUNCATE vs DROP
6. GROUP BY
7. CASE
8. CTE
9. CTE vs subquery
10. Window functions
11. ROW_NUMBER vs RANK vs DENSE_RANK
12. LAG vs LEAD
13. View vs table
14. Clustered vs non-clustered index
15. Composite index
16. Why indexes improve reads but may slow writes
17. Find second highest salary
18. Find top N records per group
19. Find latest record per customer
20. Find customers with no orders
21. Calculate month-over-month growth
22. Calculate running total
23. Find duplicate records
24. Find employees above department average

* * *

# 17. Golden Rule

Do not memorize code without understanding the business question.

For every problem, ask:

**What does the business want to know?**

Then determine:

**What data → what cleaning → what transformation → what calculation → what validation → what insight?**

That is the core workflow of a Data Analyst.
