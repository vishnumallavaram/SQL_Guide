# Batch 1 --- Video 1: Creating Tables and Inserting Data

## Learning objectives

By completing this exercise, students will practise:

-   Creating tables without constraints.
-   Selecting suitable SQL data types.
-   Inserting complete records.
-   Inserting multiple records in one statement.
-   Inserting records into selected columns.
-   Working with `NULL`, dates, decimals, and Boolean values.
-   Validating inserted records.

## Environment

Use **Databricks SQL Editor**.

> **Platform note:** The tutorial playlist covers SQL Server, while this
> exercise uses Databricks SQL. Use Databricks-compatible syntax. This
> exercise intentionally does not use primary keys, foreign keys, unique
> constraints, check constraints, default constraints, or identity
> columns.

## Student instructions

1.  Create each table using the schema below.
2.  Do not add constraints.
3.  Insert the sample data supplied for each table.
4.  Complete the INSERT practice tasks.
5.  Verify the inserted records with SELECT queries.
6.  Try the tasks yourself before checking the answer key at the bottom.

------------------------------------------------------------------------

# Part A --- Create the tables

Create the following five tables. The table schemas are requirements,
not SQL statements.

## 1. `customers`

Purpose: Store customer profile information.

  Column             Data type
  ------------------ -----------
  customer_id        INT
  customer_name      STRING
  email              STRING
  phone              STRING
  city               STRING
  state              STRING
  signup_date        DATE
  customer_segment   STRING
  is_active          BOOLEAN

## 2. `products`

Purpose: Store product catalogue information.

  Column           Data type
  ---------------- ---------------
  product_id       INT
  product_name     STRING
  category         STRING
  brand            STRING
  unit_price       DECIMAL(10,2)
  stock_quantity   INT
  launch_date      DATE
  is_available     BOOLEAN

## 3. `stores`

Purpose: Store retail store details.

  Column            Data type
  ----------------- -----------
  store_id          INT
  store_name        STRING
  city              STRING
  state             STRING
  store_type        STRING
  opening_date      DATE
  floor_area_sqft   INT

## 4. `employees`

Purpose: Store employee information.

  Column          Data type
  --------------- ---------------
  employee_id     INT
  employee_name   STRING
  department      STRING
  job_title       STRING
  salary          DECIMAL(12,2)
  hire_date       DATE
  manager_id      INT
  is_active       BOOLEAN

## 5. `orders`

Purpose: Store customer purchase transactions.

  Column           Data type
  ---------------- ---------------
  order_id         INT
  customer_id      INT
  store_id         INT
  order_date       DATE
  order_status     STRING
  payment_method   STRING
  total_amount     DECIMAL(12,2)

------------------------------------------------------------------------

# Part B --- Sample data

Use the following sample records. Write the INSERT statements yourself.

## 1. Customers --- `customers`

  -------------------------------------------------------------------------------------------------------------------------------------------
    customer_id customer_name   email               phone               city         state       signup_date   customer_segment   is_active
  ------------- --------------- ------------------- ------------------- ------------ ----------- ------------- ------------------ -----------
            101 Rahul Sharma    rahul@example.com   9876543210          Hyderabad    Telangana   2025-01-15    Premium            true

            102 Priya Reddy     priya@example.com   9876543211          Chennai      Tamil Nadu  2025-02-20    Regular            true

            103 Arjun Kumar     arjun@example.com   arjun@example.com   9876543212   Bengaluru   Karnataka     2025-03-10         Regular
  -------------------------------------------------------------------------------------------------------------------------------------------

**Data correction for student use:** For customer 103, use
`arjun@example.com` as email and `9876543212` as phone.

## 2. Products --- `products`

  ----------------------------------------------------------------------------------------------------------------
    product_id product_name   category      brand         unit_price   stock_quantity launch_date   is_available
  ------------ -------------- ------------- ----------- ------------ ---------------- ------------- --------------
           201 Wireless Mouse Electronics   Logitech          799.99               50 2025-01-05    true

           202 Office Chair   Furniture     UrbanWork        5499.50               20 2025-02-12    true

           203 Water Bottle   Accessories   AquaPure          349.00                0 2025-03-18    false
  ----------------------------------------------------------------------------------------------------------------

## 3. Stores --- `stores`

  -----------------------------------------------------------------------------------------------
      store_id store_name   city        state       store_type   opening_date     floor_area_sqft
  ------------ ------------ ----------- ----------- ------------ -------------- -----------------
           301 Central      Hyderabad   Telangana   Mall         2022-06-15                  5000
               Retail                                                           

           302 City Mart    Chennai     Tamil Nadu  High Street  2023-01-20                  3500

           303 Metro Store  Bengaluru   Karnataka   Mall         2024-04-10                  6200
  -----------------------------------------------------------------------------------------------

## 4. Employees --- `employees`

  --------------------------------------------------------------------------------------------------------
    employee_id employee_name   department   job_title        salary hire_date      manager_id is_active
  ------------- --------------- ------------ ------------ ---------- ------------ ------------ -----------
            401 Kiran Rao       Sales        Sales          35000.00 2023-05-10            410 true
                                             Executive                                         

            402 Sneha Patel     Finance      Accountant     48000.50 2022-08-15            411 true

            403 Amit Verma      IT           Data Analyst   65000.00 2024-02-01           NULL false
  --------------------------------------------------------------------------------------------------------

## 5. Orders --- `orders`

  -------------------------------------------------------------------------------------------------
     order_id   customer_id    store_id order_date   order_status   payment_method     total_amount
  ----------- ------------- ----------- ------------ -------------- ---------------- --------------
          501           101         301 2025-04-01   Delivered      Credit Card             6299.49

          502           102         302 2025-04-02   Processing     UPI                      799.99

          503           103         303 2025-04-05   Cancelled      Debit Card               349.00
  -------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

# Part C --- INSERT practice tasks

## Task 1: Insert complete records

Insert all three records into each table, supplying values for every
column.

## Task 2: Insert selected columns

Insert one additional customer by specifying only these columns:

-   `customer_id`
-   `customer_name`
-   `city`
-   `state`

Choose a new ID and your own values. Then inspect the values in the
columns you did not specify.

## Task 3: Insert multiple rows in one statement

Insert all three product records in a single INSERT statement.

## Task 4: Practise different INSERT approaches

Write and execute separate statements to:

1.  Insert one complete row.
2.  Insert multiple rows in one statement.
3.  Insert into selected columns.
4.  Insert a row with a `NULL` value where the column allows it.

## Task 5: Validate your results

-   Retrieve all rows from each table.
-   Check the record count for each table.
-   Confirm that dates, decimals, and Boolean values were inserted
    correctly.
-   Check the selected-column insert and observe the unspecified
    columns.
-   Confirm that the employee with no manager has a `NULL` `manager_id`.

------------------------------------------------------------------------

# Answer Key

> **Instructor note:** The solutions are below the student exercises.
> Students should attempt the exercises before opening this section. Run
> the statements in order. If you have already inserted the sample rows,
> avoid rerunning the INSERT statements unless you clear the practice
> tables first.

## Answer 1 --- Create tables

``` sql
CREATE TABLE customers (
  customer_id INT,
  customer_name STRING,
  email STRING,
  phone STRING,
  city STRING,
  state STRING,
  signup_date DATE,
  customer_segment STRING,
  is_active BOOLEAN
);

CREATE TABLE products (
  product_id INT,
  product_name STRING,
  category STRING,
  brand STRING,
  unit_price DECIMAL(10,2),
  stock_quantity INT,
  launch_date DATE,
  is_available BOOLEAN
);

CREATE TABLE stores (
  store_id INT,
  store_name STRING,
  city STRING,
  state STRING,
  store_type STRING,
  opening_date DATE,
  floor_area_sqft INT
);

CREATE TABLE employees (
  employee_id INT,
  employee_name STRING,
  department STRING,
  job_title STRING,
  salary DECIMAL(12,2),
  hire_date DATE,
  manager_id INT,
  is_active BOOLEAN
);

CREATE TABLE orders (
  order_id INT,
  customer_id INT,
  store_id INT,
  order_date DATE,
  order_status STRING,
  payment_method STRING,
  total_amount DECIMAL(12,2)
);
```

## Answer 2 --- Insert complete customer records

``` sql
INSERT INTO customers VALUES
(101, 'Rahul Sharma', 'rahul@example.com', '9876543210',
 'Hyderabad', 'Telangana', DATE'2025-01-15', 'Premium', true),
(102, 'Priya Reddy', 'priya@example.com', '9876543211',
 'Chennai', 'Tamil Nadu', DATE'2025-02-20', 'Regular', true),
(103, 'Arjun Kumar', 'arjun@example.com', '9876543212',
 'Bengaluru', 'Karnataka', DATE'2025-03-10', 'Regular', false);
```

## Answer 3 --- Insert product records in one statement

``` sql
INSERT INTO products VALUES
(201, 'Wireless Mouse', 'Electronics', 'Logitech', 799.99, 50,
 DATE'2025-01-05', true),
(202, 'Office Chair', 'Furniture', 'UrbanWork', 5499.50, 20,
 DATE'2025-02-12', true),
(203, 'Water Bottle', 'Accessories', 'AquaPure', 349.00, 0,
 DATE'2025-03-18', false);
```

## Answer 4 --- Insert store records

``` sql
INSERT INTO stores VALUES
(301, 'Central Retail', 'Hyderabad', 'Telangana', 'Mall',
 DATE'2022-06-15', 5000),
(302, 'City Mart', 'Chennai', 'Tamil Nadu', 'High Street',
 DATE'2023-01-20', 3500),
(303, 'Metro Store', 'Bengaluru', 'Karnataka', 'Mall',
 DATE'2024-04-10', 6200);
```

## Answer 5 --- Insert employee records

``` sql
INSERT INTO employees VALUES
(401, 'Kiran Rao', 'Sales', 'Sales Executive', 35000.00,
 DATE'2023-05-10', 410, true),
(402, 'Sneha Patel', 'Finance', 'Accountant', 48000.50,
 DATE'2022-08-15', 411, true),
(403, 'Amit Verma', 'IT', 'Data Analyst', 65000.00,
 DATE'2024-02-01', NULL, false);
```

## Answer 6 --- Insert order records

``` sql
INSERT INTO orders VALUES
(501, 101, 301, DATE'2025-04-01', 'Delivered', 'Credit Card', 6299.49),
(502, 102, 302, DATE'2025-04-02', 'Processing', 'UPI', 799.99),
(503, 103, 303, DATE'2025-04-05', 'Cancelled', 'Debit Card', 349.00);
```

## Answer 7 --- Insert selected columns

``` sql
INSERT INTO customers (customer_id, customer_name, city, state)
VALUES (104, 'Meera Iyer', 'Pune', 'Maharashtra');
```

The unspecified columns will normally be `NULL` because no default
constraints or generated values were defined and the columns are
nullable. Inspect the row to verify.

## Answer 8 --- Insert one additional row with a NULL value

``` sql
INSERT INTO employees
(employee_id, employee_name, department, job_title, salary,
 hire_date, manager_id, is_active)
VALUES
(404, 'Nikhil Das', 'Operations', 'Coordinator', 42000.00,
 DATE'2025-05-12', NULL, true);
```

## Answer 9 --- Validate the data

``` sql
SELECT * FROM customers;
SELECT * FROM products;
SELECT * FROM stores;
SELECT * FROM employees;
SELECT * FROM orders;

SELECT COUNT(*) AS customer_count FROM customers;
SELECT COUNT(*) AS product_count FROM products;
SELECT COUNT(*) AS store_count FROM stores;
SELECT COUNT(*) AS employee_count FROM employees;
SELECT COUNT(*) AS order_count FROM orders;
```

If all original sample rows and the two extra practice rows have been
inserted, the expected counts are:

  Table         Expected count
  ----------- ----------------
  customers                  4
  products                   3
  stores                     3
  employees                  4
  orders                     3

## Key learning points

-   `CREATE TABLE` defines a table's structure.
-   `INSERT INTO ... VALUES` inserts rows.
-   A single INSERT can contain multiple value rows.
-   Naming columns allows values to be supplied for only a subset of
    columns.
-   The order of values must match the order of the specified columns.
-   `NULL` represents a missing or unknown value; it is not the same as
    the string `'NULL'`.
-   Dates and decimals should use compatible values and data types.
-   Because these tables have no constraints, Databricks will not
    automatically enforce business rules such as unique IDs or valid
    customer references.

**End of Video 1 practice.**
