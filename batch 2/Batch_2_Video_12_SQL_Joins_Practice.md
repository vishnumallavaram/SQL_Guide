
# Batch 2 – Video 12: Types of Joins in SQL Server

## 1. Learning Objectives

By the end of this practice session, students should be able to:

1. Understand why SQL JOINs are required.
2. Understand the relationship between two tables.
3. Write INNER JOIN queries.
4. Write LEFT OUTER JOIN queries.
5. Write RIGHT OUTER JOIN queries.
6. Write FULL OUTER JOIN queries.
7. Write CROSS JOIN queries.
8. Understand the purpose of the ON clause.
9. Identify matching and non-matching records.
10. Choose the correct JOIN for real-world requirements.

---

# PART 1 – INTRODUCTION TO JOINS

## 2. What is a JOIN?

A JOIN is used to retrieve data from two or more tables based on a related column.

In a relational database, information is commonly stored in separate tables.

For example:

An employee table stores employee information.

A department table stores department information.

If we want to display an employee's name along with their department name, we need to combine these two tables.

We can achieve this using JOIN.

### Example tables

**tblEmployee**

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---:|
| 101 | Arun | 1 |
| 102 | Bala | 2 |
| 103 | Charan | 1 |
| 104 | Deepa | 3 |
| 105 | Esha | 5 |

**tblDepartment**

| DepartmentId | DepartmentName |
|---:|---|
| 1 | IT |
| 2 | HR |
| 3 | Finance |
| 4 | Sales |

Notice:

- Employees 101, 102, 103 and 104 have matching department IDs.
- Employee 105 has DepartmentId 5, which does not exist in tblDepartment.
- Department 4 does not have any employees.

These unmatched records will help us understand the different JOIN types.

---

# PART 2 – CREATE TABLES AND INSERT DATA

## 3. Create tblDepartment

```sql
CREATE TABLE tblDepartment
(
    DepartmentId INT PRIMARY KEY,
    DepartmentName VARCHAR(50)
);
```

### Insert sample data

```sql
INSERT INTO tblDepartment
(DepartmentId, DepartmentName)
VALUES
(1, 'IT'),
(2, 'HR'),
(3, 'Finance'),
(4, 'Sales');
```

## 4. Create tblEmployee

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    DepartmentId INT
);
```

### Insert sample data

```sql
INSERT INTO tblEmployee
(EmployeeId, EmployeeName, DepartmentId)
VALUES
(101, 'Arun', 1),
(102, 'Bala', 2),
(103, 'Charan', 1),
(104, 'Deepa', 3),
(105, 'Esha', 5),
(106, 'Farhan', 2),
(107, 'Gita', NULL);
```

### Verify the tables

```sql
SELECT * FROM tblEmployee;

SELECT * FROM tblDepartment;
```

### Understand the sample data

| Employee | DepartmentId | Situation |
|---|---:|---|
| Arun | 1 | Matching department |
| Bala | 2 | Matching department |
| Charan | 1 | Matching department |
| Deepa | 3 | Matching department |
| Esha | 5 | No matching department |
| Farhan | 2 | Matching department |
| Gita | NULL | No department assigned |

Department 4 (Sales) has no employees.

Important: The DepartmentId column in tblEmployee is intentionally not defined as a foreign key. This allows us to insert unmatched department IDs and NULL values for JOIN practice.

---

# PART 3 – INNER JOIN

## 5. What is INNER JOIN?

INNER JOIN returns only the records that have matching values in both tables.

If a record does not have a matching record in the other table, it will not appear in the result.

### Syntax

```sql
SELECT
    Column1,
    Column2
FROM Table1
INNER JOIN Table2
    ON Table1.CommonColumn = Table2.CommonColumn;
```

### Example 1: Display employee names and department names

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### Expected result

| EmployeeId | EmployeeName | DepartmentName |
|---:|---|---|
| 101 | Arun | IT |
| 102 | Bala | HR |
| 103 | Charan | IT |
| 104 | Deepa | Finance |
| 106 | Farhan | HR |

Notice:

- Esha is excluded because DepartmentId 5 does not exist.
- Gita is excluded because her DepartmentId is NULL.
- Sales is not displayed because no employee belongs to it.

### Example 2: Display employees belonging to IT

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'IT';
```

Expected result:

| EmployeeName | DepartmentName |
|---|---|
| Arun | IT |
| Charan | IT |

### When should we use INNER JOIN?

Use INNER JOIN when we need only records that have matching information in both tables.

Real-world examples:

- Employees with valid departments.
- Orders with matching customers.
- Students with registered courses.
- Products with matching categories.

---

# PART 4 – LEFT OUTER JOIN

## 6. What is LEFT JOIN?

LEFT JOIN returns:

1. All records from the left table.
2. Matching records from the right table.
3. NULL values for right-table columns when no match exists.

LEFT JOIN and LEFT OUTER JOIN mean the same thing in SQL Server.

### Syntax

```sql
SELECT
    Column1,
    Column2
FROM Table1
LEFT JOIN Table2
    ON Table1.CommonColumn = Table2.CommonColumn;
```

### Example 1: Display all employees with department information

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### Expected result

| EmployeeId | EmployeeName | DepartmentId | DepartmentName |
|---:|---|---:|---|
| 101 | Arun | 1 | IT |
| 102 | Bala | 2 | HR |
| 103 | Charan | 1 | IT |
| 104 | Deepa | 3 | Finance |
| 105 | Esha | 5 | NULL |
| 106 | Farhan | 2 | HR |
| 107 | Gita | NULL | NULL |

Notice:

- All seven employees are displayed.
- Esha is displayed even though her department does not exist.
- Gita is displayed even though she has no department assigned.
- The unmatched department information appears as NULL.

### Example 2: Find employees without a matching department

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL;
```

Expected result:

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---:|
| 105 | Esha | 5 |
| 107 | Gita | NULL |

This is a common real-world data-quality check.

### When should we use LEFT JOIN?

Use LEFT JOIN when all records from the first table are important, even if matching records are unavailable in the second table.

Real-world examples:

- All employees, including employees without departments.
- All customers, including customers without orders.
- All students, including students without course registrations.

---

# PART 5 – RIGHT OUTER JOIN

## 7. What is RIGHT JOIN?

RIGHT JOIN returns:

1. All records from the right table.
2. Matching records from the left table.
3. NULL values for left-table columns when no match exists.

RIGHT JOIN and RIGHT OUTER JOIN mean the same thing.

### Syntax

```sql
SELECT
    Column1,
    Column2
FROM Table1
RIGHT JOIN Table2
    ON Table1.CommonColumn = Table2.CommonColumn;
```

### Example 1: Display all departments and their employees

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### Expected result

| EmployeeId | EmployeeName | DepartmentId | DepartmentName |
|---:|---|---:|---|
| 101 | Arun | 1 | IT |
| 103 | Charan | 1 | IT |
| 102 | Bala | 2 | HR |
| 106 | Farhan | 2 | HR |
| 104 | Deepa | 3 | Finance |
| NULL | NULL | 4 | Sales |

Notice:

- All four departments are displayed.
- Sales is displayed even though it has no employees.
- Esha and Gita are excluded because the right table is tblDepartment and they have no matching department.

### Example 2: Find departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL;
```

Expected result:

| DepartmentId | DepartmentName |
|---:|---|
| 4 | Sales |

### When should we use RIGHT JOIN?

Use RIGHT JOIN when all records from the right-side table must be returned.

In practice, many developers prefer rewriting RIGHT JOIN as LEFT JOIN by reversing the table order.

Equivalent query:

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId;
```

Both queries return the same matching and unmatched department records.

---

# PART 6 – FULL OUTER JOIN

## 8. What is FULL OUTER JOIN?

FULL OUTER JOIN returns:

1. All matching records from both tables.
2. All non-matching records from the left table.
3. All non-matching records from the right table.

Where a match is unavailable, the columns from the missing side contain NULL.

### Syntax

```sql
SELECT
    Column1,
    Column2
FROM Table1
FULL OUTER JOIN Table2
    ON Table1.CommonColumn = Table2.CommonColumn;
```

### Example 1: Display all employees and all departments

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### Expected result

| EmployeeId | EmployeeName | EmployeeDepartmentId | MasterDepartmentId | DepartmentName |
|---:|---|---:|---:|---|
| 101 | Arun | 1 | 1 | IT |
| 103 | Charan | 1 | 1 | IT |
| 102 | Bala | 2 | 2 | HR |
| 106 | Farhan | 2 | 2 | HR |
| 104 | Deepa | 3 | 3 | Finance |
| 105 | Esha | 5 | NULL | NULL |
| 107 | Gita | NULL | NULL | NULL |
| NULL | NULL | NULL | 4 | Sales |

Notice:

- All matching employee and department records are returned.
- Esha is returned even though her department is missing.
- Gita is returned even though her department is NULL.
- Sales is returned even though it has no employees.

### Example 2: Find unmatched records from either table

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL
   OR D.DepartmentId IS NULL;
```

Expected result:

- Esha – department ID 5 does not exist in the department table.
- Gita – no department assigned.
- Sales – no employees assigned.

### When should we use FULL OUTER JOIN?

Use FULL OUTER JOIN when we need all records from both tables, including unmatched records.

Real-world examples:

- Comparing employee data with department master data.
- Identifying missing records between two systems.
- Reconciling source and target datasets.
- Finding records present in one table but missing in another.

---

# PART 7 – CROSS JOIN

## 9. What is CROSS JOIN?

CROSS JOIN produces a Cartesian product.

Every row from the first table is combined with every row from the second table.

It does not require an ON condition.

### Syntax

```sql
SELECT
    Column1,
    Column2
FROM Table1
CROSS JOIN Table2;
```

### Example 1: Create all employee and department combinations

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
CROSS JOIN tblDepartment D;
```

Our sample data contains:

- 7 employees.
- 4 departments.

Total combinations:

7 × 4 = 28 rows.

### Sample output

| EmployeeName | DepartmentName |
|---|---|
| Arun | IT |
| Arun | HR |
| Arun | Finance |
| Arun | Sales |
| Bala | IT |
| Bala | HR |
| Bala | Finance |
| Bala | Sales |
| ... | ... |

The full output contains 28 combinations.

### Example 2: Understand the Cartesian product with smaller tables

```sql
CREATE TABLE tblColor
(
    ColorId INT,
    ColorName VARCHAR(20)
);

CREATE TABLE tblSize
(
    SizeId INT,
    SizeName VARCHAR(20)
);
```

Insert data:

```sql
INSERT INTO tblColor
VALUES
(1, 'Red'),
(2, 'Blue');

INSERT INTO tblSize
VALUES
(1, 'Small'),
(2, 'Medium'),
(3, 'Large');
```

Query:

```sql
SELECT
    C.ColorName,
    S.SizeName
FROM tblColor C
CROSS JOIN tblSize S;
```

Expected result:

| ColorName | SizeName |
|---|---|
| Red | Small |
| Red | Medium |
| Red | Large |
| Blue | Small |
| Blue | Medium |
| Blue | Large |

Total rows = 2 × 3 = 6.

### When should we use CROSS JOIN?

Real-world examples:

- Generating all product and size combinations.
- Generating all color and product combinations.
- Creating combinations for test data.
- Generating a date and category combination matrix.

Important: CROSS JOIN can generate a very large number of rows. Use it carefully.

---

# PART 8 – JOIN COMPARISON

## 10. Difference Between All Five JOIN Types

| JOIN Type | Records returned |
|---|---|
| INNER JOIN | Only matching records |
| LEFT JOIN | All left records and matching right records |
| RIGHT JOIN | All right records and matching left records |
| FULL OUTER JOIN | All records from both tables |
| CROSS JOIN | Every possible combination |

### Quick memory trick

- INNER = Only matching.
- LEFT = Everything from left.
- RIGHT = Everything from right.
- FULL = Everything from both.
- CROSS = Every possible combination.

### Understanding the results with our sample data

| JOIN | Number of rows |
|---|---:|
| INNER JOIN | 5 |
| LEFT JOIN | 7 |
| RIGHT JOIN | 6 |
| FULL OUTER JOIN | 8 |
| CROSS JOIN | 28 |

These counts are based on the sample data and the join condition `Employee.DepartmentId = Department.DepartmentId`.

---

# PART 9 – GENERIC JOIN QUERY FORMULA

## 11. Standard JOIN Query Structure

```sql
SELECT
    Table1.ColumnName,
    Table2.ColumnName
FROM Table1
JOIN Table2
    ON Table1.CommonColumn = Table2.CommonColumn;
```

### Step-by-step approach

1. Identify the two tables.
2. Identify the related columns.
3. Decide which JOIN type is required.
4. Write the FROM clause.
5. Add the JOIN type and second table.
6. Write the ON condition.
7. Select the required columns.
8. Add WHERE or ORDER BY if needed.

### Example

Requirement:

Display all employees along with their department names, including employees without a matching department.

Step 1: Tables – tblEmployee and tblDepartment.

Step 2: Common column – DepartmentId.

Step 3: Requirement says all employees must appear.

Step 4: Choose LEFT JOIN.

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

---

# PART 10 – IMPORTANT JOIN CONCEPTS

## 12. Understanding Table Aliases

Aliases make JOIN queries shorter and easier to read.

Without aliases:

```sql
SELECT
    tblEmployee.EmployeeName,
    tblDepartment.DepartmentName
FROM tblEmployee
INNER JOIN tblDepartment
    ON tblEmployee.DepartmentId = tblDepartment.DepartmentId;
```

With aliases:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

Both queries produce the same result.

## 13. Understanding the ON Clause

The ON clause defines how records from the tables should match.

Example:

```sql
ON E.DepartmentId = D.DepartmentId
```

This tells SQL Server to match rows where both DepartmentId values are equal.

Without the correct join condition, the query may return incorrect or excessive rows.

## 14. JOIN Condition vs WHERE Condition

These clauses have different purposes.

- ON defines how two tables are related.
- WHERE filters the resulting records.

Example:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'IT';
```

The ON condition matches employees with departments.

The WHERE condition keeps only IT department records.

### Important: LEFT JOIN and WHERE

Consider:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'IT';
```

This returns only matching IT employees. The WHERE condition removes rows where DepartmentName is NULL.

If the requirement is to keep every employee but display department details only for IT, move the filter into ON:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
   AND D.DepartmentName = 'IT';
```

This keeps every employee and shows a department name only when the matching department is IT.

---

# PART 11 – HANDS-ON PRACTICE EXERCISES

Try solving these questions before checking the answer key.

## Level 1 – Beginner

### Scenario 1: INNER JOIN

1. Display employee names with department names.
2. Display employee IDs, names and department IDs for matching records.
3. Display only IT employees with their department names.
4. Display employees belonging to HR.
5. Display employees belonging to Finance.
6. Display employee names and department names sorted alphabetically.
7. Display all matching employee records with department information.
8. Count employees in each department using INNER JOIN and GROUP BY.

### Scenario 2: LEFT JOIN

1. Display all employees with their department names.
2. Find employees who do not have a matching department.
3. Display all employees from Hyderabad if a City column is added to the employee table.
4. Display employees and their department IDs, including NULL departments.
5. Count employees by department while retaining departments with zero employees using the department table as the left table.
6. Display all employees, sorted by department name.
7. Display employees whose department name is NULL.
8. Find all employees with or without a department match.

### Scenario 3: RIGHT JOIN

1. Display all departments with their employees.
2. Find departments without employees.
3. Display department names and employee counts.
4. Display all departments even if no employee belongs to them.
5. Find departments that have no matching employee.
6. Display departments and employee names sorted by department.
7. Find all departments with matching and non-matching employee records.
8. Rewrite the RIGHT JOIN query using LEFT JOIN.

### Scenario 4: FULL OUTER JOIN

1. Display all employees and all departments.
2. Find employee records without a department match.
3. Find departments without employees.
4. Display all matching and non-matching records.
5. Identify employee department IDs that do not exist in the department master.
6. Identify departments that do not have employees.
7. Display all records with both employee and department IDs.
8. Generate a reconciliation report between employee and department data.

### Scenario 5: CROSS JOIN

1. Display every employee and department combination.
2. Calculate the total number of combinations.
3. Display every color and size combination using tblColor and tblSize.
4. Explain why CROSS JOIN does not need an ON clause.
5. What happens if the first table has 10 rows and the second has 5 rows?
6. Create a CROSS JOIN between tblEmployee and tblDepartment.
7. Explain why CROSS JOIN can generate a large number of rows.
8. Identify two real-world use cases for CROSS JOIN.

---

## Level 2 – Intermediate

### Scenario 6: JOIN with WHERE

1. Display IT employees using INNER JOIN.
2. Display all employees but show department information only for HR.
3. Display employees who belong to departments other than IT.
4. Display employees with department information where DepartmentId is greater than 1.
5. Display all employees except those with no department match.
6. Display all departments and only employees whose IDs are greater than 103.
7. Display employees who belong to Finance or HR.
8. Compare the effect of filtering a LEFT JOIN in ON versus WHERE.

### Scenario 7: JOIN with GROUP BY

1. Count employees in each department.
2. Calculate average employees' department-wise salary after adding Salary to tblEmployee.
3. Count employees by department and display only departments having more than one employee.
4. Display all departments and their employee counts, including departments with zero employees.
5. Find departments with more than one employee.
6. Display department-wise employee counts in descending order.
7. Find departments with no employees.
8. Display total employee count for all matched departments.

---

## Level 3 – Real-World Scenarios

### Scenario 8: Employee and Department Reporting

An organization wants to generate employee and department reports.

1. Display employee names with department names.
2. Identify employees whose department is missing.
3. Identify departments that do not have employees.
4. Generate a complete employee-department mapping report.
5. Generate a report showing all departments, including those with no employees.
6. Generate a report showing all employees, including those without departments.
7. Count employees by department.
8. Generate a data-quality report showing unmatched employee records.

### Scenario 9: Data Reconciliation

Two systems contain employee and department information.

1. Identify employees with valid department IDs.
2. Identify employees with invalid department IDs.
3. Identify employees without a department ID.
4. Identify departments without employees.
5. Display all records from both tables.
6. Compare employee and department IDs.
7. Explain when FULL OUTER JOIN is more useful than INNER JOIN.
8. Explain why unmatched records are important in data migration.

### Scenario 10: Challenge Questions

1. Display only employees who have matching departments and belong to IT or HR.
2. Display all departments with employee counts, including zero.
3. Find employees whose DepartmentId does not exist in tblDepartment.
4. Find departments without employees using LEFT JOIN.
5. Write the same department report using RIGHT JOIN and LEFT JOIN.
6. Generate all employee and department combinations.
7. Find the total number of rows generated by CROSS JOIN.
8. Create a FULL OUTER JOIN reconciliation report showing unmatched records from both tables.

---

# PART 12 – ANSWER KEY

Attempt the exercises independently before referring to the solutions.

## Answers – Scenario 1: INNER JOIN

### 1. Employee names with department names

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 2. Employee IDs, names and department IDs

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentId
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 3. IT employees

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'IT';
```

### 4. HR employees

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'HR';
```

### 5. Finance employees

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'Finance';
```

### 6. Sort employee names alphabetically

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
ORDER BY E.EmployeeName;
```

### 7. All matching employee records

```sql
SELECT
    E.*,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 8. Count employees by department

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
GROUP BY D.DepartmentName;
```

---

## Answers – Scenario 2: LEFT JOIN

### 1. All employees with department names

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 2. Employees without matching departments

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL;
```

### 3. Employees from Hyderabad

The original employee table does not contain a City column. After adding it, use:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.City = 'Hyderabad';
```

### 4. All employees with department IDs

```sql
SELECT
    E.EmployeeName,
    E.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 5. Department counts including zero employees

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 6. All employees sorted by department

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
ORDER BY D.DepartmentName;
```

### 7. Employees whose department name is NULL

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName IS NULL;
```

### 8. All employees with or without department matches

```sql
SELECT
    E.*,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

---

## Answers – Scenario 3: RIGHT JOIN

### 1. All departments with employees

```sql
SELECT
    D.DepartmentName,
    E.EmployeeName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 2. Departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 3. Employee count by department

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
GROUP BY D.DepartmentName;
```

### 4. All departments including departments without employees

```sql
SELECT
    D.*,
    E.EmployeeName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 5. Departments without matching employees

```sql
SELECT
    D.DepartmentName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 6. Departments sorted by department name

```sql
SELECT
    D.DepartmentName,
    E.EmployeeName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
ORDER BY D.DepartmentName;
```

### 7. All departments with matching and non-matching employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName,
    E.EmployeeId,
    E.EmployeeName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 8. Rewrite RIGHT JOIN using LEFT JOIN

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName,
    E.EmployeeId,
    E.EmployeeName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId;
```

---

## Answers – Scenario 4: FULL OUTER JOIN

### 1. All employees and departments

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 2. Employee records without department matches

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL
  AND E.EmployeeId IS NOT NULL;
```

### 3. Departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL
  AND D.DepartmentId IS NOT NULL;
```

### 4. All matching and non-matching records

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 5. Invalid employee department IDs

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 6. Departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 7. Display both employee and department IDs

```sql
SELECT
    E.EmployeeId,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 8. Reconciliation report

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName,
    CASE
        WHEN E.EmployeeId IS NULL
            THEN 'Department Without Employee'
        WHEN E.DepartmentId IS NULL
            THEN 'Employee Without Department'
        WHEN D.DepartmentId IS NULL
            THEN 'Invalid Department ID'
        ELSE 'Matched'
    END AS ReconciliationStatus
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

---

## Answers – Scenario 5: CROSS JOIN

### 1. Every employee and department combination

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
CROSS JOIN tblDepartment D;
```

### 2. Total number of combinations

```sql
SELECT
    (SELECT COUNT(*) FROM tblEmployee) *
    (SELECT COUNT(*) FROM tblDepartment)
    AS TotalCombinations;
```

Expected result: 28.

### 3. Every color and size combination

```sql
SELECT
    C.ColorName,
    S.SizeName
FROM tblColor C
CROSS JOIN tblSize S;
```

### 4. Why no ON clause?

CROSS JOIN combines every row from the first table with every row from the second table. It does not match rows using a common column.

### 5. Ten rows and five rows

10 × 5 = 50 rows.

### 6. Employee and department combinations

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
CROSS JOIN tblDepartment D;
```

### 7. Why can CROSS JOIN generate many rows?

Because every record from the first table is combined with every record from the second table.

For example:

1000 × 500 = 500000 rows.

### 8. Real-world examples

- Generate all product and size combinations.
- Generate all color and product combinations.

---

## Answers – Scenario 6: JOIN with WHERE

### 1. IT employees

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'IT';
```

### 2. All employees, department information only for HR

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
   AND D.DepartmentName = 'HR';
```

### 3. Employees belonging to departments other than IT

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName <> 'IT';
```

### 4. Employees with DepartmentId greater than 1

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId > 1;
```

### 5. All employees except those without department matches

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 6. All departments and employees whose IDs are greater than 103

```sql
SELECT
    D.DepartmentName,
    E.EmployeeName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
   AND E.EmployeeId > 103;
```

### 7. Employees in Finance or HR

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName IN ('Finance', 'HR');
```

### 8. Compare ON and WHERE

Filter in ON:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
   AND D.DepartmentName = 'IT';
```

Filter in WHERE:

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName = 'IT';
```

The first query retains all employees. The second returns only employees matching IT.

---

## Answers – Scenario 7: JOIN with GROUP BY

For salary-related exercises, add a Salary column first:

```sql
ALTER TABLE tblEmployee
ADD Salary DECIMAL(10,2);
```

Populate sample salaries:

```sql
UPDATE tblEmployee
SET Salary =
    CASE EmployeeId
        WHEN 101 THEN 65000
        WHEN 102 THEN 45000
        WHEN 103 THEN 80000
        WHEN 104 THEN 55000
        WHEN 105 THEN 50000
        WHEN 106 THEN 90000
        WHEN 107 THEN 70000
    END;
```

### 1. Count employees in each department

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 2. Average salary by department

```sql
SELECT
    D.DepartmentName,
    AVG(E.Salary) AS AverageSalary
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 3. Departments having more than one employee

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName
HAVING COUNT(E.EmployeeId) > 1;
```

### 4. All departments including zero employees

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 5. Departments with more than one employee

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName
HAVING COUNT(E.EmployeeId) > 1;
```

### 6. Department-wise counts in descending order

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName
ORDER BY TotalEmployees DESC;
```

### 7. Departments without employees

```sql
SELECT
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 8. Total employees in matched departments

```sql
SELECT
    COUNT(E.EmployeeId) AS TotalMatchedEmployees
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

---

## Answers – Scenario 8: Employee and Department Reporting

### 1. Employee names with department names

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 2. Employees whose department is missing

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL;
```

### 3. Departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 4. Complete employee-department mapping

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 5. All departments including zero employees

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 6. All employees including those without departments

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 7. Count employees by department

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 8. Data-quality report

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

---

## Answers – Scenario 9: Data Reconciliation

### 1. Employees with valid department IDs

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 2. Employees with invalid department IDs

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 3. Employees without a department ID

```sql
SELECT
    EmployeeId,
    EmployeeName
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

### 4. Departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 5. All records from both tables

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 6. Compare employee and department IDs

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 7. Why is FULL OUTER JOIN useful?

INNER JOIN only returns matching records.

FULL OUTER JOIN also displays unmatched records from both tables, making it useful for data reconciliation.

### 8. Why are unmatched records important in migration?

They help identify:

- Missing master records.
- Invalid foreign-key values.
- Missing source records.
- Records that need correction before loading.
- Differences between source and target systems.

---

## Answers – Scenario 10: Challenge Questions

### 1. IT or HR employees

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentName IN ('IT', 'HR');
```

### 2. All departments with employee counts including zero

```sql
SELECT
    D.DepartmentName,
    COUNT(E.EmployeeId) AS TotalEmployees
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
GROUP BY D.DepartmentName;
```

### 3. Employees whose department ID does not exist

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 4. Departments without employees

```sql
SELECT
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 5. Same department report using RIGHT JOIN and LEFT JOIN

Using RIGHT JOIN:

```sql
SELECT
    D.DepartmentName,
    E.EmployeeName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

Using LEFT JOIN:

```sql
SELECT
    D.DepartmentName,
    E.EmployeeName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId;
```

### 6. All employee and department combinations

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
CROSS JOIN tblDepartment D;
```

### 7. Total CROSS JOIN rows

```sql
SELECT
    COUNT(*) AS TotalCombinations
FROM tblEmployee E
CROSS JOIN tblDepartment D;
```

Expected result: 28.

### 8. FULL OUTER JOIN reconciliation report

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId AS EmployeeDepartmentId,
    D.DepartmentId AS MasterDepartmentId,
    D.DepartmentName,
    CASE
        WHEN E.EmployeeId IS NULL
            THEN 'Department Without Employee'
        WHEN E.DepartmentId IS NULL
            THEN 'Employee Without Department'
        WHEN D.DepartmentId IS NULL
            THEN 'Invalid Department ID'
        ELSE 'Matched'
    END AS ReconciliationStatus
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

---

# PART 13 – REVISION AND INTERVIEW QUESTIONS

1. What is a JOIN in SQL Server?
2. Why do we need JOINs?
3. What is the difference between INNER JOIN and LEFT JOIN?
4. What is the difference between LEFT JOIN and RIGHT JOIN?
5. What is the purpose of FULL OUTER JOIN?
6. What is a Cartesian product?
7. Does CROSS JOIN require an ON clause?
8. What is the purpose of the ON clause?
9. Can we join more than two tables?
10. What happens when there is no matching record?
11. How can we identify unmatched records?
12. What is the difference between ON and WHERE?
13. Can a LEFT JOIN behave like an INNER JOIN?
14. Why are table aliases useful?
15. What is the difference between NULL and a missing matching row?
16. Why is COUNT(JoinedTable.PrimaryKey) useful with LEFT JOIN?
17. What is the difference between a primary key and a foreign key?
18. Why is FULL OUTER JOIN useful in data reconciliation?
19. Can CROSS JOIN produce millions of rows?
20. How would you identify invalid department IDs?

---

# PART 14 – FINAL PRACTICAL CHECKLIST

- [ ] Understand why JOINs are needed.
- [ ] Create tblEmployee and tblDepartment.
- [ ] Insert matching and non-matching records.
- [ ] Write INNER JOIN queries.
- [ ] Write LEFT JOIN queries.
- [ ] Write RIGHT JOIN queries.
- [ ] Write FULL OUTER JOIN queries.
- [ ] Write CROSS JOIN queries.
- [ ] Understand the ON clause.
- [ ] Use table aliases.
- [ ] Combine JOIN with WHERE.
- [ ] Combine JOIN with GROUP BY.
- [ ] Identify unmatched records.
- [ ] Create a reconciliation report.
- [ ] Complete all practice scenarios independently.

---

# PART 15 – OFFICIAL MICROSOFT DOCUMENTATION

1. FROM and JOIN syntax:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/from-transact-sql

2. SELECT statement:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql

3. WHERE clause:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/where-transact-sql

4. GROUP BY clause:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-group-by-transact-sql

---

## IMPORTANT LEARNING NOTES

Remember:

- INNER JOIN = Matching records only.
- LEFT JOIN = All left records plus matching right records.
- RIGHT JOIN = All right records plus matching left records.
- FULL OUTER JOIN = All records from both tables.
- CROSS JOIN = Every possible combination.

### Most important JOIN pattern

```sql
SELECT
    A.ColumnName,
    B.ColumnName
FROM TableA A
INNER JOIN TableB B
    ON A.CommonColumn = B.CommonColumn;
```

Learn this structure first. Then practise changing INNER JOIN to LEFT, RIGHT, FULL OUTER and CROSS JOIN.

**Final goal:** Students should be able to look at a business requirement, identify which records must be retained, select the correct JOIN type, and write the query independently.
