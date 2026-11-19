
# Batch 1 – Video 10: SELECT Statement in SQL Server

## 1. Learning Objectives

By completing this lesson, you will understand:

1. Selecting specific columns or all columns.
2. Retrieving distinct records using DISTINCT.
3. Filtering records using WHERE.
4. Using comparison operators.
5. Filtering records using IN and BETWEEN.
6. Searching records using LIKE and wildcards.
7. Combining conditions using AND and OR.
8. Sorting records using ORDER BY.
9. Retrieving TOP N and TOP N PERCENT records.
10. Using fully qualified table names.

---

# 2. Prepare the Practice Environment

We will use three independent SQL Server tables:

- tblPerson
- tblEmployee
- tblProduct

Execute the following scripts in SQL Server Management Studio (SSMS).

## 2.1 Create tblPerson

```sql
CREATE TABLE tblPerson
(
    PersonId INT PRIMARY KEY,
    Name NVARCHAR(50),
    Email NVARCHAR(100),
    City NVARCHAR(50),
    Age INT
);

INSERT INTO tblPerson
(PersonId, Name, Email, City, Age)
VALUES
(1, 'Sam', 'sam@gmail.com', 'London', 25),
(2, 'Sara', 'sara@gmail.com', 'New York', 30),
(3, 'John', 'john@gmail.com', 'London', 28),
(4, 'David', 'david@gmail.com', 'Hyderabad', 35),
(5, 'Priya', 'priya@gmail.com', 'Chennai', 26),
(6, 'Ravi', 'ravi@gmail.com', 'Hyderabad', 32),
(7, 'Kiran', 'kiran@gmail.com', 'Chennai', 22),
(8, 'Sam', 'sam2@gmail.com', 'London', 25),
(9, 'Anil', 'anil@gmail.com', 'Pune', 40),
(10, 'Meena', 'meena@gmail.com', 'New York', 29);
```

## 2.2 Create tblEmployee

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName NVARCHAR(50),
    Department NVARCHAR(50),
    Salary DECIMAL(10,2),
    City NVARCHAR(50)
);

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
(110, 'Karthik', 'Sales', 75000, 'Chennai');
```

## 2.3 Create tblProduct

```sql
CREATE TABLE tblProduct
(
    ProductId INT PRIMARY KEY,
    ProductName NVARCHAR(50),
    Category NVARCHAR(50),
    Price DECIMAL(10,2),
    Stock INT
);

INSERT INTO tblProduct
(ProductId, ProductName, Category, Price, Stock)
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

Verify the data:

```sql
SELECT * FROM tblPerson;
SELECT * FROM tblEmployee;
SELECT * FROM tblProduct;
```

---

# 3. SELECT – Selecting Specific or All Columns

The SELECT statement retrieves data from database tables.

### Syntax

```sql
SELECT Column_List
FROM Table_Name;
```

### Example: Select Specific Columns

```sql
SELECT Name, Email
FROM tblPerson;
```

Only Name and Email columns are returned.

### Example: Select All Columns

```sql
SELECT *
FROM tblPerson;
```

The asterisk (*) retrieves all columns.

### Difference

| SELECT Specific Columns | SELECT * |
|---|---|
| Returns selected columns | Returns all columns |
| Makes the required output clear | Useful for quick exploration |
| Avoids retrieving unnecessary columns | May retrieve unnecessary data |

**Best Practice:** Prefer explicitly specifying the required columns in production queries.

## Fully Qualified Table Names

SQL Server supports fully qualified table names.

Format:

```sql
DatabaseName.SchemaName.TableName
```

Example:

```sql
SELECT *
FROM TrainingDB.dbo.tblPerson;
```

Here:

- TrainingDB = Database name
- dbo = Schema name
- tblPerson = Table name

Replace TrainingDB with your actual database name.

---

# 4. DISTINCT – Selecting Unique Records

DISTINCT removes duplicate rows from the result set.

### Syntax

```sql
SELECT DISTINCT Column_Name
FROM Table_Name;
```

### Example: Unique Cities

```sql
SELECT DISTINCT City
FROM tblPerson;
```

Expected cities:

- London
- New York
- Hyderabad
- Chennai
- Pune

### DISTINCT on Multiple Columns

```sql
SELECT DISTINCT City, Age
FROM tblPerson;
```

DISTINCT checks the complete combination of selected columns.

For example, two rows are duplicates only if both City and Age match.

### Practice

1. Retrieve unique cities from tblPerson.
2. Retrieve unique departments from tblEmployee.
3. Retrieve unique product categories.
4. Retrieve unique combinations of City and Age.
5. Retrieve unique combinations of Department and City.

---

# 5. WHERE – Filtering Records

WHERE filters records based on specified conditions.

### Syntax

```sql
SELECT Column_List
FROM Table_Name
WHERE Filter_Condition;
```

Example:

```sql
SELECT Name, Email
FROM tblPerson
WHERE City = 'London';
```

### Comparison Operators

| Operator | Meaning |
|---|---|
| = | Equal to |
| <> | Not equal to |
| != | Not equal to |
| > | Greater than |
| < | Less than |
| >= | Greater than or equal to |
| <= | Less than or equal to |

### Examples

**Equal to**

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT';
```

**Not equal to**

```sql
SELECT *
FROM tblEmployee
WHERE Department <> 'IT';
```

**Greater than**

```sql
SELECT *
FROM tblEmployee
WHERE Salary > 60000;
```

**Less than**

```sql
SELECT *
FROM tblEmployee
WHERE Salary < 50000;
```

**Greater than or equal to**

```sql
SELECT *
FROM tblEmployee
WHERE Salary >= 65000;
```

**Less than or equal to**

```sql
SELECT *
FROM tblEmployee
WHERE Salary <= 50000;
```

**Important:**

- Text values should be enclosed in single quotes.
- Numeric values do not require quotes.

Examples:

```sql
WHERE City = 'London'

WHERE Age > 25

WHERE Salary >= 50000
```

---

# 6. IN Operator

IN checks whether a value matches any value in a specified list.

### Syntax

```sql
SELECT Column_List
FROM Table_Name
WHERE Column_Name IN (Value1, Value2, Value3);
```

Example:

Find employees working in IT or HR.

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('IT', 'HR');
```

Equivalent query:

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT'
OR Department = 'HR';
```

### Practice

1. Find people living in London, Chennai or Hyderabad.
2. Find employees working in HR or Finance.
3. Find products belonging to Electronics or Furniture.
4. Find employees with EmployeeId 101, 105 or 110.

---

# 7. BETWEEN Operator

BETWEEN retrieves records within a specified range.

### Syntax

```sql
SELECT Column_List
FROM Table_Name
WHERE Column_Name BETWEEN Value1 AND Value2;
```

Example:

Find employees earning between 50000 and 80000.

```sql
SELECT *
FROM tblEmployee
WHERE Salary BETWEEN 50000 AND 80000;
```

BETWEEN is inclusive. Both boundary values are included.

Equivalent query:

```sql
SELECT *
FROM tblEmployee
WHERE Salary >= 50000
AND Salary <= 80000;
```

### Practice

1. Find people whose age is between 25 and 35.
2. Find employees earning between 45000 and 70000.
3. Find products priced between 1000 and 15000.
4. Find products with stock between 10 and 50.

---

# 8. LIKE Operator and Wildcards

LIKE is used to search for specific text patterns.

| Wildcard | Meaning |
|---|---|
| % | Zero or more characters |
| _ | Exactly one character |
| [] | Any one character from the specified set |
| [^] | Any one character not in the specified set |

## Percentage (%) Wildcard

Names starting with S:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S%';
```

Names ending with a:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%a';
```

Names containing ar:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%ar%';
```

Names starting with Sa:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'Sa%';
```

## Underscore (_) Wildcard

Exactly three characters, starting with any character and ending with am:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '_am';
```

Names starting with S, followed by any one character, ending with m:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S_m';
```

## Square Brackets []

Names starting with S or R:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '[SR]%';
```

Names starting with a character between A and D:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '[A-D]%';
```

## Excluding Characters [^]

Names not starting with S:

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '[^S]%';
```

**Note:** LIKE matching can be case-sensitive or case-insensitive depending on the column collation.

---

# 9. AND and OR Operators

AND and OR combine multiple filtering conditions.

## AND Operator

All specified conditions must be TRUE.

Example:

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT'
AND Salary > 60000;
```

## OR Operator

At least one condition must be TRUE.

Example:

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT'
OR Department = 'HR';
```

## Combining AND and OR

Example:

Find IT employees earning more than 60000 OR employees working in HR.

```sql
SELECT *
FROM tblEmployee
WHERE (Department = 'IT' AND Salary > 60000)
OR Department = 'HR';
```

### Understanding Parentheses

Consider:

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT'
OR Department = 'HR'
AND Salary > 60000;
```

SQL Server evaluates AND before OR.

It is interpreted as:

```sql
WHERE Department = 'IT'
OR (Department = 'HR' AND Salary > 60000);
```

If you want both IT and HR employees to earn more than 60000:

```sql
SELECT *
FROM tblEmployee
WHERE (Department = 'IT' OR Department = 'HR')
AND Salary > 60000;
```

**Best Practice:** Use parentheses when combining AND and OR.

---

# 10. ORDER BY – Sorting Records

ORDER BY sorts records in ascending or descending order.

## Ascending Order

```sql
SELECT *
FROM tblEmployee
ORDER BY Salary ASC;
```

ASC sorts numeric values from lowest to highest and text values alphabetically.

## Descending Order

```sql
SELECT *
FROM tblEmployee
ORDER BY Salary DESC;
```

DESC sorts numeric values from highest to lowest.

## Sorting by Multiple Columns

```sql
SELECT *
FROM tblEmployee
ORDER BY Department ASC, Salary DESC;
```

First sort by Department, then sort each department by Salary descending.

## Sorting by Column Position

```sql
SELECT EmployeeName, Salary, Department
FROM tblEmployee
ORDER BY 2 DESC;
```

Here, 2 represents Salary because it is the second column in the SELECT list.

**Best Practice:** Prefer column names over column positions for readability.

---

# 11. TOP – Selecting Top N Records

TOP retrieves a specified number of records.

## TOP 5

```sql
SELECT TOP (5) *
FROM tblEmployee;
```

Without ORDER BY, SQL Server does not guarantee which five records will be returned.

## Five Highest-Paid Employees

```sql
SELECT TOP (5) *
FROM tblEmployee
ORDER BY Salary DESC;
```

## Three Cheapest Products

```sql
SELECT TOP (3) *
FROM tblProduct
ORDER BY Price ASC;
```

## TOP N PERCENT

```sql
SELECT TOP (10) PERCENT *
FROM tblEmployee
ORDER BY Salary DESC;
```

This retrieves approximately the top 10 percent of employees.

SQL Server rounds a fractional row count up to the next whole row.

## WITH TIES

WITH TIES includes additional records that have the same ORDER BY value as the last selected record.

```sql
SELECT TOP (3) WITH TIES *
FROM tblEmployee
ORDER BY Salary DESC;
```

**Important:** WITH TIES requires ORDER BY.

---

# 12. Basic Practice Scenarios

## Scenario 1: Person Information

Using tblPerson:

1. Retrieve all columns.
2. Retrieve only Name and Email.
3. Retrieve all unique cities.
4. Find people living in Hyderabad.
5. Find people aged above 30.
6. Find people aged between 25 and 35.
7. Find people living in Chennai or Pune.
8. Find names starting with S.
9. Find names ending with a.
10. Sort people by Age descending.

## Scenario 2: Employee Analysis

Using tblEmployee:

1. Retrieve all employees.
2. Retrieve EmployeeName and Salary.
3. Find unique departments.
4. Find employees working in IT.
5. Find employees earning more than 60000.
6. Find employees earning between 50000 and 80000.
7. Find employees working in IT, HR or Finance.
8. Find employees whose names start with A or K.
9. Find employees earning more than 60000 and working in IT.
10. Display the five highest-paid employees.

## Scenario 3: Product Analysis

Using tblProduct:

1. Retrieve all products.
2. Retrieve ProductName and Price.
3. Find unique product categories.
4. Find products belonging to Electronics.
5. Find products priced above 10000.
6. Find products priced between 1000 and 15000.
7. Find products belonging to Accessories or Furniture.
8. Find products whose names start with M.
9. Find products with stock less than 20.
10. Display the three most expensive products.

---

# 13. Additional Hands-on Exercises – Level 2

**Instructions:**

- Use the same three tables and sample data from Section 2.
- Do not create additional tables.
- Solve the questions independently.
- Execute your queries in SSMS.
- Check the answer key only after attempting the exercises.

## Scenario 1: Person Data Analysis

Using tblPerson:

1. Display the names and cities of people whose age is greater than 25.
2. Find people younger than 30 who live in London.
3. Find people living in Hyderabad or Chennai and older than 25.
4. Find people whose names contain the letter 'a'.
5. Find people whose names start with S and whose age is greater than 20.
6. Display people aged between 25 and 35, sorted by age descending.
7. Find people who do not live in London or Chennai.
8. Retrieve the three oldest people.
9. Retrieve unique combinations of City and Age.
10. Find people whose names contain exactly four characters.

## Scenario 2: Employee Salary Analysis

Using tblEmployee:

1. Find employees earning more than 60000.
2. Find employees earning between 45000 and 75000.
3. Find employees working in IT or Finance.
4. Find employees who are not working in HR.
5. Find employees working in Hyderabad with salary greater than 50000.
6. Find employees working in IT or HR whose salary is greater than 60000.
7. Find employees whose names start with a letter between A and G.
8. Display the three highest-paid employees.
9. Display the second-highest salary and employee details.
10. Display employees sorted by Department and then Salary descending.

## Scenario 3: Product Inventory Analysis

Using tblProduct:

1. Find products priced above 10000.
2. Find products with stock below 20.
3. Find Electronics products priced below 30000.
4. Find products belonging to Accessories or Furniture.
5. Find products priced between 1000 and 15000.
6. Find products whose names start with M or P.
7. Find products whose names contain the letter 'o'.
8. Find products with stock greater than 20 and price less than 10000.
9. Display the five most expensive products.
10. Display products sorted by Category ascending and Price descending.

## Scenario 4: Combined AND / OR Logic

Using tblEmployee:

1. Find IT employees earning more than 70000 OR Finance employees earning more than 60000.
2. Find employees working in IT or HR AND earning at least 50000.
3. Find employees working in Hyderabad AND earning more than 50000 OR employees working in Chennai AND earning more than 80000.
4. Find employees whose department is neither IT nor HR.
5. Find employees earning between 50000 and 80000 who belong to Finance or Sales.

## Scenario 5: Wildcard Challenge

Using tblPerson:

1. Find names starting with S.
2. Find names ending with a.
3. Find names containing 'ar'.
4. Find names containing exactly three characters.
5. Find names starting with a character between A and M.
6. Find names not starting with S.
7. Find names whose second character is a.
8. Find names that start with either S or K.

## Scenario 6: TOP and Sorting Challenge

Using tblEmployee and tblProduct:

1. Find the highest-paid employee.
2. Find the lowest-paid employee.
3. Find the three lowest-paid employees.
4. Find the top 50 percent of employees based on salary.
5. Find the most expensive product.
6. Find the three cheapest products.
7. Find the top 30 percent of products based on price.
8. Display employees in descending salary order and ascending name order.
9. Find the top two highest-paid employees.
10. Find the top three employees by salary, including all employees tied with the third employee's salary.

## Scenario 7: Business Reporting Queries

Write SQL queries for these requirements:

1. Retrieve all departments without duplicates.
2. Retrieve all product categories without duplicates.
3. Display employees earning more than the average salary.
4. Display products with stock greater than 10 and price greater than 5000.
5. Display employees working in Chennai or Hyderabad, sorted by salary descending.
6. Retrieve people aged between 25 and 35 who live in London or Hyderabad.
7. Display the top five products by price.
8. Retrieve employees whose names contain 'a' and whose salary is above 50000.

Note: Question 3 is an additional challenge involving a subquery and aggregate function.

## Scenario 8: Find the Correct Query

For each requirement, write the correct SQL query.

1. Retrieve only EmployeeName and Department.
2. Retrieve unique cities from tblPerson.
3. Find products that are not in Electronics.
4. Find employees earning exactly 50000.
5. Find people whose names start with S and end with m.
6. Find employees working in Finance or Sales with salary greater than 60000.
7. Retrieve the top four employees based on salary.
8. Find products with a price between 500 and 12000.
9. Retrieve unique combinations of Product Category and Price.
10. Find employees whose names have at least four characters and start with A.

## Scenario 9: Debug the SQL Queries

Each query contains a mistake. Identify and correct it.

**Question 1**

```sql
SELECT Name Email
FROM tblPerson;
```

**Question 2**

```sql
SELECT *
FROM tblEmployee
WHERE Department = IT;
```

**Question 3**

```sql
SELECT *
FROM tblProduct
WHERE Price BETWEEN 15000 AND 1000;
```

**Question 4**

Requirement: Find names starting with S, regardless of length.

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S_';
```

**Question 5**

Requirement: Display employees earning the highest salary first.

```sql
SELECT *
FROM tblEmployee
ORDER BY Salary;
```

## Scenario 10: Final SQL Challenge

Use all three tables.

1. Find people aged above 25 who live in Hyderabad or London, sorted by age descending.
2. Find employees in IT or Finance earning between 55000 and 90000.
3. Find the two highest-priced Electronics products.
4. Find employees whose names start with A, C or D.
5. Find people whose names contain 'a' and whose age is below 30.
6. Find products that are not in Furniture and have stock greater than 20.
7. Display the top 20 percent of employees by salary.
8. Find unique combinations of Employee Department and City.
9. Find the three oldest people, including ties at the third person's age.
10. Find employees earning more than 50000, not from Chennai, and belonging to IT or Finance.

---

# 14. Answer Key – Basic Practice Scenarios

## Scenario 1: Person Information

**1. All columns**

```sql
SELECT *
FROM tblPerson;
```

**2. Name and Email**

```sql
SELECT Name, Email
FROM tblPerson;
```

**3. Unique cities**

```sql
SELECT DISTINCT City
FROM tblPerson;
```

**4. People living in Hyderabad**

```sql
SELECT *
FROM tblPerson
WHERE City = 'Hyderabad';
```

**5. People older than 30**

```sql
SELECT *
FROM tblPerson
WHERE Age > 30;
```

**6. Age between 25 and 35**

```sql
SELECT *
FROM tblPerson
WHERE Age BETWEEN 25 AND 35;
```

**7. People in Chennai or Pune**

```sql
SELECT *
FROM tblPerson
WHERE City IN ('Chennai', 'Pune');
```

**8. Names starting with S**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S%';
```

**9. Names ending with a**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%a';
```

**10. Sort by Age descending**

```sql
SELECT *
FROM tblPerson
ORDER BY Age DESC;
```

## Scenario 2: Employee Analysis

**1. All employees**

```sql
SELECT *
FROM tblEmployee;
```

**2. EmployeeName and Salary**

```sql
SELECT EmployeeName, Salary
FROM tblEmployee;
```

**3. Unique departments**

```sql
SELECT DISTINCT Department
FROM tblEmployee;
```

**4. IT employees**

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT';
```

**5. Salary above 60000**

```sql
SELECT *
FROM tblEmployee
WHERE Salary > 60000;
```

**6. Salary between 50000 and 80000**

```sql
SELECT *
FROM tblEmployee
WHERE Salary BETWEEN 50000 AND 80000;
```

**7. IT, HR or Finance**

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('IT', 'HR', 'Finance');
```

**8. Names starting with A or K**

```sql
SELECT *
FROM tblEmployee
WHERE EmployeeName LIKE '[AK]%';
```

**9. IT employees earning above 60000**

```sql
SELECT *
FROM tblEmployee
WHERE Salary > 60000
AND Department = 'IT';
```

**10. Five highest-paid employees**

```sql
SELECT TOP (5) *
FROM tblEmployee
ORDER BY Salary DESC;
```

## Scenario 3: Product Analysis

**1. All products**

```sql
SELECT *
FROM tblProduct;
```

**2. ProductName and Price**

```sql
SELECT ProductName, Price
FROM tblProduct;
```

**3. Unique categories**

```sql
SELECT DISTINCT Category
FROM tblProduct;
```

**4. Electronics products**

```sql
SELECT *
FROM tblProduct
WHERE Category = 'Electronics';
```

**5. Price above 10000**

```sql
SELECT *
FROM tblProduct
WHERE Price > 10000;
```

**6. Price between 1000 and 15000**

```sql
SELECT *
FROM tblProduct
WHERE Price BETWEEN 1000 AND 15000;
```

**7. Accessories or Furniture**

```sql
SELECT *
FROM tblProduct
WHERE Category IN ('Accessories', 'Furniture');
```

**8. Product names starting with M**

```sql
SELECT *
FROM tblProduct
WHERE ProductName LIKE 'M%';
```

**9. Stock below 20**

```sql
SELECT *
FROM tblProduct
WHERE Stock < 20;
```

**10. Three most expensive products**

```sql
SELECT TOP (3) *
FROM tblProduct
ORDER BY Price DESC;
```

---

# 15. Answer Key – Additional Hands-on Exercises

## Scenario 1: Person Data Analysis

**1. People older than 25**

```sql
SELECT Name, City
FROM tblPerson
WHERE Age > 25;
```

**2. People younger than 30 living in London**

```sql
SELECT *
FROM tblPerson
WHERE Age < 30
AND City = 'London';
```

**3. People in Hyderabad or Chennai and older than 25**

```sql
SELECT *
FROM tblPerson
WHERE City IN ('Hyderabad', 'Chennai')
AND Age > 25;
```

**4. Names containing a**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%a%';
```

**5. Names starting with S and age greater than 20**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S%'
AND Age > 20;
```

**6. Age between 25 and 35, descending**

```sql
SELECT *
FROM tblPerson
WHERE Age BETWEEN 25 AND 35
ORDER BY Age DESC;
```

**7. People not living in London or Chennai**

```sql
SELECT *
FROM tblPerson
WHERE City NOT IN ('London', 'Chennai');
```

**8. Three oldest people**

```sql
SELECT TOP (3) *
FROM tblPerson
ORDER BY Age DESC;
```

**9. Unique City and Age combinations**

```sql
SELECT DISTINCT City, Age
FROM tblPerson;
```

**10. Names containing exactly four characters**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '____';
```

## Scenario 2: Employee Salary Analysis

**1. Employees earning more than 60000**

```sql
SELECT *
FROM tblEmployee
WHERE Salary > 60000;
```

**2. Employees earning between 45000 and 75000**

```sql
SELECT *
FROM tblEmployee
WHERE Salary BETWEEN 45000 AND 75000;
```

**3. IT or Finance employees**

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('IT', 'Finance');
```

**4. Employees not working in HR**

```sql
SELECT *
FROM tblEmployee
WHERE Department <> 'HR';
```

**5. Hyderabad employees earning above 50000**

```sql
SELECT *
FROM tblEmployee
WHERE City = 'Hyderabad'
AND Salary > 50000;
```

**6. IT or HR employees earning above 60000**

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('IT', 'HR')
AND Salary > 60000;
```

**7. Names starting between A and G**

```sql
SELECT *
FROM tblEmployee
WHERE EmployeeName LIKE '[A-G]%';
```

**8. Three highest-paid employees**

```sql
SELECT TOP (3) *
FROM tblEmployee
ORDER BY Salary DESC;
```

**9. Second-highest salary and employee details**

```sql
SELECT *
FROM tblEmployee
ORDER BY Salary DESC, EmployeeId ASC
OFFSET 1 ROWS
FETCH NEXT 1 ROWS ONLY;
```

**10. Department ascending and salary descending**

```sql
SELECT *
FROM tblEmployee
ORDER BY Department ASC, Salary DESC;
```

## Scenario 3: Product Inventory Analysis

**1. Products priced above 10000**

```sql
SELECT *
FROM tblProduct
WHERE Price > 10000;
```

**2. Stock below 20**

```sql
SELECT *
FROM tblProduct
WHERE Stock < 20;
```

**3. Electronics products priced below 30000**

```sql
SELECT *
FROM tblProduct
WHERE Category = 'Electronics'
AND Price < 30000;
```

**4. Accessories or Furniture**

```sql
SELECT *
FROM tblProduct
WHERE Category IN ('Accessories', 'Furniture');
```

**5. Products priced between 1000 and 15000**

```sql
SELECT *
FROM tblProduct
WHERE Price BETWEEN 1000 AND 15000;
```

**6. Names starting with M or P**

```sql
SELECT *
FROM tblProduct
WHERE ProductName LIKE 'M%'
OR ProductName LIKE 'P%';
```

**7. Names containing o**

```sql
SELECT *
FROM tblProduct
WHERE ProductName LIKE '%o%';
```

**8. Stock above 20 and price below 10000**

```sql
SELECT *
FROM tblProduct
WHERE Stock > 20
AND Price < 10000;
```

**9. Five most expensive products**

```sql
SELECT TOP (5) *
FROM tblProduct
ORDER BY Price DESC;
```

**10. Category ascending and price descending**

```sql
SELECT *
FROM tblProduct
ORDER BY Category ASC, Price DESC;
```

## Scenario 4: Combined AND / OR Logic

**1. IT above 70000 OR Finance above 60000**

```sql
SELECT *
FROM tblEmployee
WHERE (Department = 'IT' AND Salary > 70000)
OR (Department = 'Finance' AND Salary > 60000);
```

**2. IT or HR with salary at least 50000**

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('IT', 'HR')
AND Salary >= 50000;
```

**3. Hyderabad above 50000 OR Chennai above 80000**

```sql
SELECT *
FROM tblEmployee
WHERE (City = 'Hyderabad' AND Salary > 50000)
OR (City = 'Chennai' AND Salary > 80000);
```

**4. Employees not in IT or HR**

```sql
SELECT *
FROM tblEmployee
WHERE Department NOT IN ('IT', 'HR');
```

**5. Salary between 50000 and 80000 in Finance or Sales**

```sql
SELECT *
FROM tblEmployee
WHERE Salary BETWEEN 50000 AND 80000
AND Department IN ('Finance', 'Sales');
```

## Scenario 5: Wildcard Challenge

**1. Names starting with S**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S%';
```

**2. Names ending with a**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%a';
```

**3. Names containing ar**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%ar%';
```

**4. Names containing exactly three characters**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '___';
```

**5. Names starting between A and M**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '[A-M]%';
```

**6. Names not starting with S**

```sql
SELECT *
FROM tblPerson
WHERE Name NOT LIKE 'S%';
```

**7. Names whose second character is a**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '_a%';
```

**8. Names starting with S or K**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '[SK]%';
```

## Scenario 6: TOP and Sorting Challenge

**1. Highest-paid employee**

```sql
SELECT TOP (1) *
FROM tblEmployee
ORDER BY Salary DESC;
```

**2. Lowest-paid employee**

```sql
SELECT TOP (1) *
FROM tblEmployee
ORDER BY Salary ASC;
```

**3. Three lowest-paid employees**

```sql
SELECT TOP (3) *
FROM tblEmployee
ORDER BY Salary ASC;
```

**4. Top 50 percent of employees**

```sql
SELECT TOP (50) PERCENT *
FROM tblEmployee
ORDER BY Salary DESC;
```

**5. Most expensive product**

```sql
SELECT TOP (1) *
FROM tblProduct
ORDER BY Price DESC;
```

**6. Three cheapest products**

```sql
SELECT TOP (3) *
FROM tblProduct
ORDER BY Price ASC;
```

**7. Top 30 percent of products**

```sql
SELECT TOP (30) PERCENT *
FROM tblProduct
ORDER BY Price DESC;
```

**8. Salary descending and name ascending**

```sql
SELECT *
FROM tblEmployee
ORDER BY Salary DESC, EmployeeName ASC;
```

**9. Two highest-paid employees**

```sql
SELECT TOP (2) *
FROM tblEmployee
ORDER BY Salary DESC;
```

**10. Three highest-paid employees including ties**

```sql
SELECT TOP (3) WITH TIES *
FROM tblEmployee
ORDER BY Salary DESC;
```

## Scenario 7: Business Reporting Queries

**1. Unique departments**

```sql
SELECT DISTINCT Department
FROM tblEmployee;
```

**2. Unique product categories**

```sql
SELECT DISTINCT Category
FROM tblProduct;
```

**3. Employees earning above average salary**

```sql
SELECT *
FROM tblEmployee
WHERE Salary > (SELECT AVG(Salary) FROM tblEmployee);
```

**4. Products with stock above 10 and price above 5000**

```sql
SELECT *
FROM tblProduct
WHERE Stock > 10
AND Price > 5000;
```

**5. Chennai or Hyderabad employees sorted by salary**

```sql
SELECT *
FROM tblEmployee
WHERE City IN ('Chennai', 'Hyderabad')
ORDER BY Salary DESC;
```

**6. People aged 25–35 in London or Hyderabad**

```sql
SELECT *
FROM tblPerson
WHERE Age BETWEEN 25 AND 35
AND City IN ('London', 'Hyderabad');
```

**7. Top five products by price**

```sql
SELECT TOP (5) *
FROM tblProduct
ORDER BY Price DESC;
```

**8. Employees with a in their name and salary above 50000**

```sql
SELECT *
FROM tblEmployee
WHERE EmployeeName LIKE '%a%'
AND Salary > 50000;
```

## Scenario 8: Find the Correct Query

**1. EmployeeName and Department**

```sql
SELECT EmployeeName, Department
FROM tblEmployee;
```

**2. Unique cities**

```sql
SELECT DISTINCT City
FROM tblPerson;
```

**3. Products not in Electronics**

```sql
SELECT *
FROM tblProduct
WHERE Category <> 'Electronics';
```

**4. Employees earning exactly 50000**

```sql
SELECT *
FROM tblEmployee
WHERE Salary = 50000;
```

**5. Names starting with S and ending with m**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S%m';
```

**6. Finance or Sales employees earning above 60000**

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('Finance', 'Sales')
AND Salary > 60000;
```

**7. Top four employees by salary**

```sql
SELECT TOP (4) *
FROM tblEmployee
ORDER BY Salary DESC;
```

**8. Products priced between 500 and 12000**

```sql
SELECT *
FROM tblProduct
WHERE Price BETWEEN 500 AND 12000;
```

**9. Unique Category and Price combinations**

```sql
SELECT DISTINCT Category, Price
FROM tblProduct;
```

**10. Employee names starting with A and at least four characters**

```sql
SELECT *
FROM tblEmployee
WHERE EmployeeName LIKE 'A%'
AND LEN(EmployeeName) >= 4;
```

## Scenario 9: Debug the SQL Queries

**Question 1 – Corrected**

```sql
SELECT Name, Email
FROM tblPerson;
```

Reason: A comma is required between column names.

**Question 2 – Corrected**

```sql
SELECT *
FROM tblEmployee
WHERE Department = 'IT';
```

Reason: Text values require single quotes.

**Question 3 – Corrected**

```sql
SELECT *
FROM tblProduct
WHERE Price BETWEEN 1000 AND 15000;
```

Reason: The intended range is from 1000 to 15000.

**Question 4 – Corrected**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE 'S%';
```

Reason: Percentage matches zero or more characters. Underscore matches exactly one character.

**Question 5 – Corrected**

```sql
SELECT *
FROM tblEmployee
ORDER BY Salary DESC;
```

Reason: DESC sorts salary from highest to lowest.

## Scenario 10: Final SQL Challenge

**1. People older than 25 in Hyderabad or London**

```sql
SELECT *
FROM tblPerson
WHERE Age > 25
AND City IN ('Hyderabad', 'London')
ORDER BY Age DESC;
```

**2. IT or Finance employees earning between 55000 and 90000**

```sql
SELECT *
FROM tblEmployee
WHERE Department IN ('IT', 'Finance')
AND Salary BETWEEN 55000 AND 90000;
```

**3. Two highest-priced Electronics products**

```sql
SELECT TOP (2) *
FROM tblProduct
WHERE Category = 'Electronics'
ORDER BY Price DESC;
```

**4. Employee names starting with A, C or D**

```sql
SELECT *
FROM tblEmployee
WHERE EmployeeName LIKE '[ACD]%';
```

**5. People containing a and younger than 30**

```sql
SELECT *
FROM tblPerson
WHERE Name LIKE '%a%'
AND Age < 30;
```

**6. Products not in Furniture with stock above 20**

```sql
SELECT *
FROM tblProduct
WHERE Category <> 'Furniture'
AND Stock > 20;
```

**7. Top 20 percent of employees**

```sql
SELECT TOP (20) PERCENT *
FROM tblEmployee
ORDER BY Salary DESC;
```

**8. Unique Department and City combinations**

```sql
SELECT DISTINCT Department, City
FROM tblEmployee;
```

**9. Three oldest people including ties**

```sql
SELECT TOP (3) WITH TIES *
FROM tblPerson
ORDER BY Age DESC;
```

**10. Employees earning above 50000, not from Chennai, in IT or Finance**

```sql
SELECT *
FROM tblEmployee
WHERE Salary > 50000
AND City <> 'Chennai'
AND Department IN ('IT', 'Finance');
```

---

# 16. Interview and Revision Questions

1. What is the purpose of SELECT?
2. What is the difference between SELECT * and selecting specific columns?
3. What is DISTINCT?
4. Does DISTINCT apply independently to every selected column?
5. What is a fully qualified table name?
6. What is the purpose of WHERE?
7. What is the difference between = and <>?
8. What is the difference between IN and OR?
9. Is BETWEEN inclusive or exclusive?
10. What is the purpose of LIKE?
11. What is the difference between % and _?
12. What is the purpose of square brackets in LIKE?
13. What is the difference between AND and OR?
14. Why do we use parentheses with AND and OR?
15. What is the purpose of ORDER BY?
16. What is the default sorting order?
17. Can ORDER BY contain multiple columns?
18. What is the purpose of TOP?
19. What is the difference between TOP 5 and TOP 5 PERCENT?
20. Why should ORDER BY be used with TOP?
21. What is the purpose of WITH TIES?

---

# 17. Final Student Self-Assessment

| Skill | Expected ability |
|---|---|
| SELECT | Retrieve required columns |
| DISTINCT | Remove duplicate result rows |
| WHERE | Filter records |
| Comparison operators | Compare values |
| IN | Match values from a list |
| BETWEEN | Filter inclusive ranges |
| LIKE | Search using patterns |
| AND / OR | Combine conditions |
| ORDER BY | Sort result sets |
| TOP | Limit returned records |
| WITH TIES | Include tied records at the TOP boundary |

## Completion Checklist

### SELECT Fundamentals

- [ ] Select specific columns.
- [ ] Select all columns.
- [ ] Understand fully qualified table names.
- [ ] Retrieve distinct records.

### Filtering

- [ ] Use WHERE.
- [ ] Practice comparison operators.
- [ ] Practice IN.
- [ ] Practice BETWEEN.
- [ ] Practice LIKE.
- [ ] Understand wildcards.

### Multiple Conditions

- [ ] Use AND.
- [ ] Use OR.
- [ ] Combine AND and OR.
- [ ] Understand parentheses and precedence.

### Sorting and TOP

- [ ] Sort using ASC.
- [ ] Sort using DESC.
- [ ] Sort using multiple columns.
- [ ] Retrieve TOP N.
- [ ] Retrieve TOP N PERCENT.
- [ ] Understand WITH TIES.

### Final Practice

- [ ] Complete basic practice scenarios.
- [ ] Complete all 10 additional hands-on scenarios.
- [ ] Execute the answer queries.
- [ ] Complete debugging exercises.
- [ ] Answer revision questions.

---

# 18. Official Microsoft Documentation

1. SELECT:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql

2. WHERE:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/where-transact-sql

3. LIKE:
   https://learn.microsoft.com/en-us/sql/t-sql/language-elements/like-transact-sql

4. ORDER BY:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-order-by-clause-transact-sql

---

# 19. Important Learning Notes

1. SELECT retrieves data from tables.
2. DISTINCT removes duplicate rows from the result.
3. WHERE filters records based on conditions.
4. IN checks whether a value matches any value in a list.
5. BETWEEN checks an inclusive range.
6. LIKE searches for text patterns using wildcards.
7. AND requires all conditions to be TRUE.
8. OR requires at least one condition to be TRUE.
9. ORDER BY sorts the result set.
10. TOP limits the number or percentage of returned rows.
11. Use ORDER BY with TOP when you need predictable ranking.

## Final Objective

By the end of Video 10, students should be comfortable writing SELECT queries to retrieve, filter, search, sort and limit data from SQL Server tables.

The objective is not just to write SQL queries.

Students should be able to understand a business requirement, translate it into SQL logic, execute the query and validate the result.
