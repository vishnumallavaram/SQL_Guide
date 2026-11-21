
# Batch 2 – Video 11: GROUP BY Clause in SQL Server

## 1. Learning Objectives

By the end of this practice session, students should be able to:

1. Understand why the GROUP BY clause is used.
2. Group records based on one column.
3. Use aggregate functions with GROUP BY.
4. Group records using multiple columns.
5. Understand the difference between WHERE and HAVING.
6. Filter records before grouping using WHERE.
7. Filter aggregated groups using HAVING.
8. Combine WHERE, GROUP BY and HAVING in a single query.
9. Create summary reports using SQL Server.

---

## 2. What is GROUP BY?

The GROUP BY clause is used to arrange rows into groups based on one or more columns.

It is commonly used with aggregate functions to calculate summary information for each group.

For example:

Imagine an employee table containing 100 employees across five departments.

Instead of displaying all 100 employee records, we may want to know:

- How many employees work in each department?
- What is the average salary in each department?
- What is the highest salary in each department?
- What is the total salary paid by each department?

We can answer these questions using GROUP BY.

### Basic syntax

```sql
SELECT Column_Name, Aggregate_Function(Column_Name)
FROM Table_Name
GROUP BY Column_Name;
```

### Important aggregate functions

| Function | Purpose |
|---|---|
| COUNT() | Counts rows or non-NULL values |
| SUM() | Calculates the total |
| AVG() | Calculates the average |
| MIN() | Returns the minimum value |
| MAX() | Returns the maximum value |

---

## 3. Create Practice Tables and Insert Sample Data

We will use three independent tables throughout this exercise.

- `tblPerson`
- `tblEmployee`
- `tblProduct`

### Step 1: Create tblPerson

```sql
CREATE TABLE tblPerson
(
    PersonId INT PRIMARY KEY,
    Name VARCHAR(50),
    City VARCHAR(50),
    Gender VARCHAR(10),
    Age INT
);
```

### Insert sample data

```sql
INSERT INTO tblPerson
(PersonId, Name, City, Gender, Age)
VALUES
(1, 'Sam', 'London', 'Male', 25),
(2, 'Sara', 'New York', 'Female', 30),
(3, 'John', 'London', 'Male', 28),
(4, 'David', 'Hyderabad', 'Male', 35),
(5, 'Priya', 'Chennai', 'Female', 26),
(6, 'Ravi', 'Hyderabad', 'Male', 32),
(7, 'Kiran', 'Chennai', 'Male', 22),
(8, 'Meena', 'New York', 'Female', 29),
(9, 'Anil', 'Pune', 'Male', 40),
(10, 'Divya', 'Pune', 'Female', 31),
(11, 'Arjun', 'London', 'Male', 27),
(12, 'Neha', 'Hyderabad', 'Female', 24);
```

### Step 2: Create tblEmployee

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    Department VARCHAR(50),
    Salary DECIMAL(10,2),
    City VARCHAR(50)
);
```

### Insert sample data

```sql
INSERT INTO tblEmployee
(EmployeeId, EmployeeName, Department, Salary, City)
VALUES
(101, 'Arun', 'IT', 65000, 'Hyderabad'),
(102, 'Bala', 'HR', 45000, 'Chennai'),
(103, 'Charan', 'IT', 80000, 'Bangalore'),
(104, 'Deepa', 'Finance', 55000, 'Hyderabad'),
(105, 'Esha', 'HR', 50000, 'Pune'),
(106, 'Farhan', 'IT', 90000, 'Chennai'),
(107, 'Gita', 'Finance', 70000, 'Bangalore'),
(108, 'Hari', 'IT', 60000, 'Hyderabad'),
(109, 'Indu', 'Sales', 48000, 'Pune'),
(110, 'Karthik', 'Sales', 75000, 'Chennai'),
(111, 'Lakshmi', 'Finance', 85000, 'Hyderabad'),
(112, 'Manoj', 'HR', 40000, 'Bangalore');
```

### Step 3: Create tblProduct

```sql
CREATE TABLE tblProduct
(
    ProductId INT PRIMARY KEY,
    ProductName VARCHAR(50),
    Category VARCHAR(50),
    Price DECIMAL(10,2),
    StockQuantity INT
);
```

### Insert sample data

```sql
INSERT INTO tblProduct
(ProductId, ProductName, Category, Price, StockQuantity)
VALUES
(1, 'Laptop', 'Electronics', 55000, 10),
(2, 'Mouse', 'Accessories', 500, 100),
(3, 'Keyboard', 'Accessories', 1200, 50),
(4, 'Monitor', 'Electronics', 15000, 20),
(5, 'Mobile', 'Electronics', 25000, 30),
(6, 'Chair', 'Furniture', 8000, 15),
(7, 'Table', 'Furniture', 12000, 8),
(8, 'Headphones', 'Accessories', 2500, 40),
(9, 'Printer', 'Electronics', 18000, 12),
(10, 'Desk Lamp', 'Furniture', 1500, 25);
```

---

# PART 1 – BASIC GROUP BY

## 4. GROUP BY Using One Column

### Example 1: Count people in each city

```sql
SELECT
    City,
    COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City;
```

Expected result:

| City | TotalPeople |
|---|---:|
| Chennai | 2 |
| Hyderabad | 3 |
| London | 3 |
| New York | 2 |
| Pune | 2 |

Note: The order of rows is not guaranteed unless ORDER BY is used.

### Example 2: Count employees in each department

```sql
SELECT
    Department,
    COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department;
```

Expected result:

| Department | TotalEmployees |
|---|---:|
| Finance | 3 |
| HR | 3 |
| IT | 4 |
| Sales | 2 |

### Example 3: Count products in each category

```sql
SELECT
    Category,
    COUNT(*) AS TotalProducts
FROM tblProduct
GROUP BY Category;
```

Expected result:

| Category | TotalProducts |
|---|---:|
| Accessories | 3 |
| Electronics | 4 |
| Furniture | 3 |

---

## 5. GROUP BY With Aggregate Functions

### Example 1: Total salary by department

```sql
SELECT
    Department,
    SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department;
```

Expected result:

| Department | TotalSalary |
|---|---:|
| Finance | 210000 |
| HR | 135000 |
| IT | 295000 |
| Sales | 123000 |

### Example 2: Average salary by department

```sql
SELECT
    Department,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department;
```

Expected result:

| Department | AverageSalary |
|---|---:|
| Finance | 70000 |
| HR | 45000 |
| IT | 73750 |
| Sales | 61500 |

### Example 3: Highest salary in each department

```sql
SELECT
    Department,
    MAX(Salary) AS HighestSalary
FROM tblEmployee
GROUP BY Department;
```

### Example 4: Lowest salary in each department

```sql
SELECT
    Department,
    MIN(Salary) AS LowestSalary
FROM tblEmployee
GROUP BY Department;
```

### Example 5: Total stock by product category

```sql
SELECT
    Category,
    SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category;
```

Expected result:

| Category | TotalStock |
|---|---:|
| Accessories | 190 |
| Electronics | 72 |
| Furniture | 48 |

---

## 6. Using Multiple Aggregate Functions

We can use multiple aggregate functions in the same GROUP BY query.

### Example: Complete department salary report

```sql
SELECT
    Department,
    COUNT(*) AS TotalEmployees,
    SUM(Salary) AS TotalSalary,
    AVG(Salary) AS AverageSalary,
    MIN(Salary) AS MinimumSalary,
    MAX(Salary) AS MaximumSalary
FROM tblEmployee
GROUP BY Department;
```

This query provides a complete salary summary for every department.

### Important difference: COUNT(*) vs COUNT(Column)

- `COUNT(*)` counts all rows.
- `COUNT(ColumnName)` counts only non-NULL values in that column.
- `SUM`, `AVG`, `MIN` and `MAX` ignore NULL values.

---

# PART 2 – GROUP BY MULTIPLE COLUMNS

## 7. Grouping by Multiple Columns

We can group records using more than one column.

For example, grouping people by City alone gives city-wise totals.

But grouping by both City and Gender gives a separate total for each city-gender combination.

### Syntax

```sql
SELECT
    Column1,
    Column2,
    Aggregate_Function(Column3)
FROM Table_Name
GROUP BY Column1, Column2;
```

### Example 1: Count people by City and Gender

```sql
SELECT
    City,
    Gender,
    COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City, Gender
ORDER BY City, Gender;
```

Expected result:

| City | Gender | TotalPeople |
|---|---|---:|
| Chennai | Female | 1 |
| Chennai | Male | 1 |
| Hyderabad | Female | 1 |
| Hyderabad | Male | 2 |
| London | Male | 3 |
| New York | Female | 2 |
| Pune | Female | 1 |
| Pune | Male | 1 |

### Example 2: Average salary by Department and City

```sql
SELECT
    Department,
    City,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department, City
ORDER BY Department, City;
```

### Example 3: Employee count by Department and City

```sql
SELECT
    Department,
    City,
    COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department, City
ORDER BY Department, City;
```

### Example 4: Total stock by Category and Product

```sql
SELECT
    Category,
    ProductName,
    SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category, ProductName;
```

Because each product name occurs once in our sample data, this example produces one group per product. The same pattern becomes more useful when multiple records share the same category and product name.

### Important rule

If a column appears in the SELECT list without an aggregate function, it must be included in the GROUP BY clause.

Incorrect:

```sql
SELECT
    Department,
    City,
    SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department;
```

Why is it incorrect?

Because City is selected but is neither grouped nor aggregated.

Correct:

```sql
SELECT
    Department,
    City,
    SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department, City;
```

---

# PART 3 – WHERE VS HAVING

## 8. Understanding WHERE

WHERE filters individual records before grouping and aggregation.

### Example: Employees earning more than 50000

```sql
SELECT *
FROM tblEmployee
WHERE Salary > 50000;
```

This returns only employee records whose salary is greater than 50000.

### Example: Count employees earning more than 50000 in each department

```sql
SELECT
    Department,
    COUNT(*) AS TotalEmployees
FROM tblEmployee
WHERE Salary > 50000
GROUP BY Department;
```

Here:

1. WHERE removes employees earning 50000 or less.
2. GROUP BY groups the remaining employees by department.
3. COUNT calculates the employee count for each department.

---

## 9. Understanding HAVING

HAVING filters groups after grouping and aggregation.

It is especially useful when we need to filter based on aggregate results.

### Example 1: Departments having more than two employees

```sql
SELECT
    Department,
    COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department
HAVING COUNT(*) > 2;
```

Expected result:

| Department | TotalEmployees |
|---|---:|
| Finance | 3 |
| HR | 3 |
| IT | 4 |

### Example 2: Departments with average salary greater than 60000

```sql
SELECT
    Department,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
HAVING AVG(Salary) > 60000;
```

Expected result:

| Department | AverageSalary |
|---|---:|
| Finance | 70000 |
| IT | 73750 |

### Example 3: Product categories with total stock greater than 50

```sql
SELECT
    Category,
    SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category
HAVING SUM(StockQuantity) > 50;
```

Expected result:

| Category | TotalStock |
|---|---:|
| Accessories | 190 |
| Electronics | 72 |

---

## 10. Difference Between WHERE and HAVING

| WHERE | HAVING |
|---|---|
| Filters individual rows | Filters grouped results |
| Applied before GROUP BY | Applied after GROUP BY |
| Normally used with regular column conditions | Commonly used with aggregate conditions |
| Cannot directly use aggregate functions in its condition | Can use aggregate functions |
| Reduces rows before aggregation | Reduces groups after aggregation |

### Easy way to remember

- WHERE = Which records should participate in grouping?
- HAVING = Which groups should appear in the final result?

---

# PART 4 – COMBINING WHERE, GROUP BY AND HAVING

## 11. Using All Three Together

This is an important real-world SQL pattern.

### Example 1: Departments having more than one employee earning above 50000

```sql
SELECT
    Department,
    COUNT(*) AS TotalEmployees,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
WHERE Salary > 50000
GROUP BY Department
HAVING COUNT(*) > 1;
```

Execution:

1. WHERE filters employees earning above 50000.
2. GROUP BY groups the remaining records by Department.
3. COUNT and AVG calculate the group-level results.
4. HAVING keeps departments with more than one qualifying employee.

### Example 2: Cities with at least two people older than 25

```sql
SELECT
    City,
    COUNT(*) AS TotalPeople
FROM tblPerson
WHERE Age > 25
GROUP BY City
HAVING COUNT(*) >= 2;
```

### Example 3: Product categories with total stock above 50, considering products priced above 1000

```sql
SELECT
    Category,
    SUM(StockQuantity) AS TotalStock
FROM tblProduct
WHERE Price > 1000
GROUP BY Category
HAVING SUM(StockQuantity) > 50;
```

---

## 12. SQL Logical Query Processing Order

A simplified logical processing order for a grouped SELECT query is:

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

Remember: this is the logical processing order, not necessarily the physical execution order chosen by the SQL Server optimizer.

### Example

```sql
SELECT
    Department,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
WHERE City = 'Hyderabad'
GROUP BY Department
HAVING AVG(Salary) > 50000
ORDER BY AverageSalary DESC;
```

Understand each step:

- FROM: Read records from tblEmployee.
- WHERE: Select Hyderabad employees.
- GROUP BY: Create department groups.
- HAVING: Keep departments with average salary above 50000.
- SELECT: Display department and average salary.
- ORDER BY: Sort by average salary in descending order.

---

# PART 5 – ADDITIONAL CONCEPTS AND BEST PRACTICES

## 13. GROUP BY and ORDER BY

GROUP BY creates groups. ORDER BY sorts the final result.

These clauses serve different purposes.

Example:

```sql
SELECT
    Department,
    SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department
ORDER BY TotalSalary DESC;
```

This displays departments from highest total salary to lowest total salary.

## 14. GROUP BY and DISTINCT

Both can produce unique combinations of values, but their purposes differ.

Using DISTINCT:

```sql
SELECT DISTINCT City
FROM tblPerson;
```

Using GROUP BY:

```sql
SELECT City
FROM tblPerson
GROUP BY City;
```

Both return one row per city in this example.

However, GROUP BY is generally used when calculating aggregate information for each group.

## 15. Handling NULL Values

GROUP BY places NULL values into a single group.

Example:

```sql
SELECT
    City,
    COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City;
```

If some records have NULL in City, those records appear together in one NULL group.

Aggregate functions such as SUM and AVG ignore NULL values in the aggregated column.

COUNT(*) still counts those rows.

## 16. Best Practices

1. Use WHERE to eliminate unnecessary records before grouping.
2. Use HAVING when filtering aggregated groups.
3. Select only required columns.
4. Give meaningful aliases to aggregate results.
5. Use ORDER BY when a specific output order is required.
6. Check whether NULL values can affect your aggregate calculations.
7. Avoid selecting columns that are neither grouped nor aggregated.
8. Understand the difference between COUNT(*) and COUNT(ColumnName).

---

# PART 6 – HANDS-ON PRACTICE EXERCISES

Try solving these questions before checking the answer key.

Use only the three tables created earlier.

## Level 1 – Beginner

### Scenario 1: Person Summary

1. Find the total number of people in each city.
2. Find the number of male and female people.
3. Find the average age of people in each city.
4. Find the youngest person age in each city.
5. Find the highest age in each city.
6. Find the total number of people in London.
7. Display city-wise counts in descending order.
8. Find cities having more than two people.

### Scenario 2: Employee Summary

1. Count employees in each department.
2. Calculate total salary by department.
3. Calculate average salary by department.
4. Find the highest salary in each department.
5. Find the lowest salary in each department.
6. Find the total salary paid to employees in Hyderabad by department.
7. Display department-wise employee counts.
8. Sort departments by average salary in descending order.

### Scenario 3: Product Summary

1. Count products in each category.
2. Calculate total stock by category.
3. Find the highest product price in each category.
4. Find the lowest product price in each category.
5. Calculate average product price by category.
6. Find categories with more than two products.
7. Display categories in descending order of total stock.
8. Calculate the total stock across all products.

---

## Level 2 – Intermediate

### Scenario 4: WHERE Practice

1. Count employees by department where salary is greater than 50000.
2. Calculate average salary by department for employees in Hyderabad.
3. Count people by city where age is greater than 25.
4. Calculate total stock by category for products priced above 1000.
5. Find average salary by department for employees not located in Chennai.

### Scenario 5: HAVING Practice

1. Find departments having more than two employees.
2. Find departments having an average salary greater than 60000.
3. Find departments having total salary greater than 150000.
4. Find cities having more than two people.
5. Find product categories having total stock greater than 50.
6. Find product categories with average price greater than 5000.
7. Find departments where the highest salary is greater than 80000.
8. Find cities where the average person age is greater than 28.

### Scenario 6: WHERE and HAVING Together

1. Find departments having more than one employee earning above 50000.
2. Find cities having at least two people older than 25.
3. Find product categories having total stock above 50, considering products priced above 1000.
4. Find departments having average salary above 60000, considering only employees in Hyderabad.
5. Find departments with more than one employee whose salary is at least 60000.
6. Find cities with at least two people whose age is below 35.
7. Find product categories with more than two products priced below 20000.
8. Find departments whose total salary exceeds 100000, considering employees earning above 45000.

---

## Level 3 – Real-World Scenarios

### Scenario 7: HR Department Reporting

An HR team wants to understand employee distribution and salary costs.

1. Generate a department-wise employee count report.
2. Find the total monthly salary cost for each department.
3. Find the average salary for each department.
4. Identify departments with more than two employees.
5. Identify departments where average salary exceeds 60000.
6. Display the department with its highest salary.
7. Find departments with total salary above 150000.
8. Display the salary report from highest to lowest average salary.

### Scenario 8: Location-Based Employee Reporting

Management wants to understand employee distribution across cities.

1. Count employees in each city.
2. Count employees by city and department.
3. Calculate average salary by city.
4. Calculate total salary by city.
5. Find cities having more than two employees.
6. Find city-department combinations having more than one employee.
7. Find cities where average salary is greater than 60000.
8. Display total salary by city in descending order.

### Scenario 9: Product Inventory Reporting

A store manager wants an inventory summary.

1. Count products in each category.
2. Calculate total stock in each category.
3. Find average price in each category.
4. Find categories with total stock above 50.
5. Find categories with average price greater than 5000.
6. Calculate total stock for products priced above 1000 by category.
7. Find categories containing more than two products priced below 20000.
8. Sort categories by total stock.

### Scenario 10: Challenge Questions

1. Find the department with the highest total salary.
2. Find the city with the highest average employee salary.
3. Find the department having the second-highest average salary.
4. Count people by City and Gender and display only groups having more than one person.
5. Find the product category with the highest total stock.
6. Find departments with at least three employees and an average salary above 50000.
7. Find the city-department combinations where total salary exceeds 100000.
8. Find product categories with average price between 1000 and 20000.

---

# PART 7 – ANSWER KEY

Attempt the exercises independently before referring to these solutions.

## Answers – Scenario 1: Person Summary

### 1. Total people in each city

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City;
```

### 2. Number of male and female people

```sql
SELECT Gender, COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY Gender;
```

### 3. Average age in each city

```sql
SELECT City, AVG(Age) AS AverageAge
FROM tblPerson
GROUP BY City;
```

### 4. Youngest age in each city

```sql
SELECT City, MIN(Age) AS YoungestAge
FROM tblPerson
GROUP BY City;
```

### 5. Highest age in each city

```sql
SELECT City, MAX(Age) AS HighestAge
FROM tblPerson
GROUP BY City;
```

### 6. Total number of people in London

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
WHERE City = 'London'
GROUP BY City;
```

### 7. City-wise counts in descending order

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City
ORDER BY TotalPeople DESC;
```

### 8. Cities having more than two people

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City
HAVING COUNT(*) > 2;
```

---

## Answers – Scenario 2: Employee Summary

### 1. Count employees in each department

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department;
```

### 2. Total salary by department

```sql
SELECT Department, SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department;
```

### 3. Average salary by department

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department;
```

### 4. Highest salary in each department

```sql
SELECT Department, MAX(Salary) AS HighestSalary
FROM tblEmployee
GROUP BY Department;
```

### 5. Lowest salary in each department

```sql
SELECT Department, MIN(Salary) AS LowestSalary
FROM tblEmployee
GROUP BY Department;
```

### 6. Total salary for Hyderabad employees by department

```sql
SELECT Department, SUM(Salary) AS TotalSalary
FROM tblEmployee
WHERE City = 'Hyderabad'
GROUP BY Department;
```

### 7. Department-wise employee counts

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department;
```

### 8. Sort departments by average salary

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
ORDER BY AverageSalary DESC;
```

---

## Answers – Scenario 3: Product Summary

### 1. Count products by category

```sql
SELECT Category, COUNT(*) AS TotalProducts
FROM tblProduct
GROUP BY Category;
```

### 2. Total stock by category

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category;
```

### 3. Highest product price by category

```sql
SELECT Category, MAX(Price) AS HighestPrice
FROM tblProduct
GROUP BY Category;
```

### 4. Lowest product price by category

```sql
SELECT Category, MIN(Price) AS LowestPrice
FROM tblProduct
GROUP BY Category;
```

### 5. Average product price by category

```sql
SELECT Category, AVG(Price) AS AveragePrice
FROM tblProduct
GROUP BY Category;
```

### 6. Categories having more than two products

```sql
SELECT Category, COUNT(*) AS TotalProducts
FROM tblProduct
GROUP BY Category
HAVING COUNT(*) > 2;
```

### 7. Categories sorted by total stock

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category
ORDER BY TotalStock DESC;
```

### 8. Total stock across all products

```sql
SELECT SUM(StockQuantity) AS OverallStock
FROM tblProduct;
```

---

## Answers – Scenario 4: WHERE Practice

### 1. Employees earning above 50000 by department

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
WHERE Salary > 50000
GROUP BY Department;
```

### 2. Average salary by department in Hyderabad

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
WHERE City = 'Hyderabad'
GROUP BY Department;
```

### 3. People older than 25 by city

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
WHERE Age > 25
GROUP BY City;
```

### 4. Total stock by category for products priced above 1000

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
WHERE Price > 1000
GROUP BY Category;
```

### 5. Average salary by department excluding Chennai

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
WHERE City <> 'Chennai'
GROUP BY Department;
```

---

## Answers – Scenario 5: HAVING Practice

### 1. Departments with more than two employees

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department
HAVING COUNT(*) > 2;
```

### 2. Departments with average salary above 60000

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
HAVING AVG(Salary) > 60000;
```

### 3. Departments with total salary above 150000

```sql
SELECT Department, SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department
HAVING SUM(Salary) > 150000;
```

### 4. Cities with more than two people

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City
HAVING COUNT(*) > 2;
```

### 5. Categories with total stock above 50

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category
HAVING SUM(StockQuantity) > 50;
```

### 6. Categories with average price above 5000

```sql
SELECT Category, AVG(Price) AS AveragePrice
FROM tblProduct
GROUP BY Category
HAVING AVG(Price) > 5000;
```

### 7. Departments with highest salary above 80000

```sql
SELECT Department, MAX(Salary) AS HighestSalary
FROM tblEmployee
GROUP BY Department
HAVING MAX(Salary) > 80000;
```

### 8. Cities with average person age above 28

```sql
SELECT City, AVG(Age) AS AverageAge
FROM tblPerson
GROUP BY City
HAVING AVG(Age) > 28;
```

---

## Answers – Scenario 6: WHERE and HAVING Together

### 1. More than one employee earning above 50000

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
WHERE Salary > 50000
GROUP BY Department
HAVING COUNT(*) > 1;
```

### 2. At least two people older than 25 in each city

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
WHERE Age > 25
GROUP BY City
HAVING COUNT(*) >= 2;
```

### 3. Stock above 50 for products priced above 1000

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
WHERE Price > 1000
GROUP BY Category
HAVING SUM(StockQuantity) > 50;
```

### 4. Hyderabad departments with average salary above 60000

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
WHERE City = 'Hyderabad'
GROUP BY Department
HAVING AVG(Salary) > 60000;
```

### 5. More than one employee earning at least 60000

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
WHERE Salary >= 60000
GROUP BY Department
HAVING COUNT(*) > 1;
```

### 6. Cities with at least two people younger than 35

```sql
SELECT City, COUNT(*) AS TotalPeople
FROM tblPerson
WHERE Age < 35
GROUP BY City
HAVING COUNT(*) >= 2;
```

### 7. Categories with more than two products priced below 20000

```sql
SELECT Category, COUNT(*) AS TotalProducts
FROM tblProduct
WHERE Price < 20000
GROUP BY Category
HAVING COUNT(*) > 2;
```

### 8. Departments with total salary above 100000 for employees earning above 45000

```sql
SELECT Department, SUM(Salary) AS TotalSalary
FROM tblEmployee
WHERE Salary > 45000
GROUP BY Department
HAVING SUM(Salary) > 100000;
```

---

## Answers – Scenario 7: HR Department Reporting

### 1. Department-wise employee count

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department;
```

### 2. Total monthly salary cost

```sql
SELECT Department, SUM(Salary) AS MonthlySalaryCost
FROM tblEmployee
GROUP BY Department;
```

### 3. Average salary by department

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department;
```

### 4. Departments with more than two employees

```sql
SELECT Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY Department
HAVING COUNT(*) > 2;
```

### 5. Departments with average salary above 60000

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
HAVING AVG(Salary) > 60000;
```

### 6. Highest salary in each department

```sql
SELECT Department, MAX(Salary) AS HighestSalary
FROM tblEmployee
GROUP BY Department;
```

### 7. Departments with total salary above 150000

```sql
SELECT Department, SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department
HAVING SUM(Salary) > 150000;
```

### 8. Salary report sorted by average salary

```sql
SELECT Department, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
ORDER BY AverageSalary DESC;
```

---

## Answers – Scenario 8: Location-Based Employee Reporting

### 1. Count employees by city

```sql
SELECT City, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY City;
```

### 2. Count employees by city and department

```sql
SELECT City, Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY City, Department;
```

### 3. Average salary by city

```sql
SELECT City, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY City;
```

### 4. Total salary by city

```sql
SELECT City, SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY City;
```

### 5. Cities with more than two employees

```sql
SELECT City, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY City
HAVING COUNT(*) > 2;
```

### 6. City-department combinations with more than one employee

```sql
SELECT City, Department, COUNT(*) AS TotalEmployees
FROM tblEmployee
GROUP BY City, Department
HAVING COUNT(*) > 1;
```

### 7. Cities with average salary above 60000

```sql
SELECT City, AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY City
HAVING AVG(Salary) > 60000;
```

### 8. Total salary by city in descending order

```sql
SELECT City, SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY City
ORDER BY TotalSalary DESC;
```

---

## Answers – Scenario 9: Product Inventory Reporting

### 1. Count products by category

```sql
SELECT Category, COUNT(*) AS TotalProducts
FROM tblProduct
GROUP BY Category;
```

### 2. Total stock by category

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category;
```

### 3. Average price by category

```sql
SELECT Category, AVG(Price) AS AveragePrice
FROM tblProduct
GROUP BY Category;
```

### 4. Categories with total stock above 50

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category
HAVING SUM(StockQuantity) > 50;
```

### 5. Categories with average price above 5000

```sql
SELECT Category, AVG(Price) AS AveragePrice
FROM tblProduct
GROUP BY Category
HAVING AVG(Price) > 5000;
```

### 6. Total stock for products priced above 1000

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
WHERE Price > 1000
GROUP BY Category;
```

### 7. Categories with more than two products priced below 20000

```sql
SELECT Category, COUNT(*) AS TotalProducts
FROM tblProduct
WHERE Price < 20000
GROUP BY Category
HAVING COUNT(*) > 2;
```

### 8. Sort categories by total stock

```sql
SELECT Category, SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category
ORDER BY TotalStock DESC;
```

---

## Answers – Scenario 10: Challenge Questions

### 1. Department with the highest total salary

```sql
SELECT TOP 1
    Department,
    SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY Department
ORDER BY TotalSalary DESC;
```

### 2. City with the highest average employee salary

```sql
SELECT TOP 1
    City,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY City
ORDER BY AverageSalary DESC;
```

### 3. Department with the second-highest average salary

```sql
SELECT TOP 1
    Department,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
ORDER BY AverageSalary DESC
OFFSET 1 ROWS;
```

### 4. City and Gender groups having more than one person

```sql
SELECT
    City,
    Gender,
    COUNT(*) AS TotalPeople
FROM tblPerson
GROUP BY City, Gender
HAVING COUNT(*) > 1;
```

### 5. Product category with the highest total stock

```sql
SELECT TOP 1
    Category,
    SUM(StockQuantity) AS TotalStock
FROM tblProduct
GROUP BY Category
ORDER BY TotalStock DESC;
```

### 6. Departments with at least three employees and average salary above 50000

```sql
SELECT
    Department,
    COUNT(*) AS TotalEmployees,
    AVG(Salary) AS AverageSalary
FROM tblEmployee
GROUP BY Department
HAVING COUNT(*) >= 3
   AND AVG(Salary) > 50000;
```

### 7. City-department combinations with total salary above 100000

```sql
SELECT
    City,
    Department,
    SUM(Salary) AS TotalSalary
FROM tblEmployee
GROUP BY City, Department
HAVING SUM(Salary) > 100000;
```

### 8. Product categories with average price between 1000 and 20000

```sql
SELECT
    Category,
    AVG(Price) AS AveragePrice
FROM tblProduct
GROUP BY Category
HAVING AVG(Price) BETWEEN 1000 AND 20000;
```

---

# PART 8 – REVISION AND INTERVIEW QUESTIONS

Try answering these without looking at the earlier explanations.

1. What is the purpose of GROUP BY?
2. Can GROUP BY be used with multiple columns?
3. What is the difference between SUM and COUNT?
4. What is the difference between COUNT(*) and COUNT(ColumnName)?
5. What is the difference between WHERE and HAVING?
6. Can we use SUM() in a WHERE condition?
7. Can we use COUNT() in a HAVING condition?
8. Why must a non-aggregated SELECT column be included in GROUP BY?
9. What happens when NULL values are grouped?
10. What is the difference between GROUP BY and DISTINCT?
11. Can we use ORDER BY with GROUP BY?
12. What is the logical processing order of WHERE, GROUP BY and HAVING?
13. Can we use WHERE and HAVING in the same query?
14. Why should we filter unnecessary records using WHERE?
15. Can we group records by three or more columns?

---

# PART 9 – FINAL PRACTICAL CHECKLIST

Students should be able to complete the following independently.

- [ ] Create the three practice tables.
- [ ] Insert the sample records.
- [ ] Use GROUP BY with one column.
- [ ] Use COUNT() with GROUP BY.
- [ ] Use SUM() with GROUP BY.
- [ ] Use AVG() with GROUP BY.
- [ ] Use MIN() and MAX() with GROUP BY.
- [ ] Group by multiple columns.
- [ ] Use WHERE before GROUP BY.
- [ ] Use HAVING after GROUP BY.
- [ ] Combine WHERE, GROUP BY and HAVING.
- [ ] Sort aggregated results using ORDER BY.
- [ ] Solve the real-world scenarios.
- [ ] Complete the challenge questions without referring to the answer key.

## Official Microsoft Documentation

1. GROUP BY:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-group-by-transact-sql

2. Aggregate Functions:
   https://learn.microsoft.com/en-us/sql/t-sql/functions/aggregate-functions-transact-sql

3. WHERE:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/where-transact-sql

4. HAVING:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-having-transact-sql

---

## Important Learning Notes

Remember these three concepts:

**GROUP BY** – Creates groups of records.

**WHERE** – Filters records before grouping.

**HAVING** – Filters groups after aggregation.

The most important pattern to remember:

```sql
SELECT
    GroupingColumn,
    COUNT(*) AS TotalRecords,
    SUM(NumericColumn) AS TotalValue,
    AVG(NumericColumn) AS AverageValue
FROM TableName
WHERE Condition
GROUP BY GroupingColumn
HAVING COUNT(*) > 1
ORDER BY TotalValue DESC;
```

Practice writing this pattern independently. It is one of the most frequently used patterns in SQL reporting and data analysis.
