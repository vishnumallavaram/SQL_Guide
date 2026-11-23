
# Batch 2 – Video 17: UNION and UNION ALL in SQL Server

## 1. Learning Objectives

By the end of this practice, you will understand:

- What UNION and UNION ALL operators do.
- How to combine results from multiple SELECT statements.
- The difference between UNION and UNION ALL.
- How duplicate records are handled.
- The requirements for using UNION operators.
- How to sort combined results.
- The difference between UNION and JOIN.
- Practical applications of UNION and UNION ALL.

---

# 2. Introduction to UNION and UNION ALL

UNION and UNION ALL are SQL Server set operators.

They combine the result sets of two or more SELECT statements into one result set.

### Basic syntax

```sql
SELECT column1, column2
FROM table1

UNION

SELECT column1, column2
FROM table2;
```

For UNION ALL:

```sql
SELECT column1, column2
FROM table1

UNION ALL

SELECT column1, column2
FROM table2;
```

### Visual understanding

Suppose we have two tables.

**Table A – tblEmployee**

| EmployeeId | EmployeeName |
|---:|---|
| 1 | Arun |
| 2 | Bala |
| 3 | Charan |

**Table B – tblFormerEmployee**

| EmployeeId | EmployeeName |
|---:|---|
| 4 | Deepa |
| 5 | Esha |
| 6 | Arun |

We want to combine the employee names from both tables.

UNION and UNION ALL allow us to do this.

---

# 3. Create Sample Tables

We will use generic employee tables throughout this lesson.

## Create tblEmployee

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT,
    EmployeeName VARCHAR(50),
    Department VARCHAR(50)
);
```

## Insert records

```sql
INSERT INTO tblEmployee
VALUES
    (1, 'Arun', 'IT'),
    (2, 'Bala', 'HR'),
    (3, 'Charan', 'Finance'),
    (4, 'Deepa', 'IT'),
    (5, 'Esha', 'Sales');
```

## Create tblFormerEmployee

```sql
CREATE TABLE tblFormerEmployee
(
    EmployeeId INT,
    EmployeeName VARCHAR(50),
    Department VARCHAR(50)
);
```

## Insert records

```sql
INSERT INTO tblFormerEmployee
VALUES
    (101, 'Arun', 'IT'),
    (102, 'Farhan', 'HR'),
    (103, 'Gita', 'Finance'),
    (104, 'Hari', 'Sales'),
    (105, 'Bala', 'HR');
```

## View both tables

```sql
SELECT *
FROM tblEmployee;

SELECT *
FROM tblFormerEmployee;
```

### tblEmployee

| EmployeeId | EmployeeName | Department |
|---:|---|---|
| 1 | Arun | IT |
| 2 | Bala | HR |
| 3 | Charan | Finance |
| 4 | Deepa | IT |
| 5 | Esha | Sales |

### tblFormerEmployee

| EmployeeId | EmployeeName | Department |
|---:|---|---|
| 101 | Arun | IT |
| 102 | Farhan | HR |
| 103 | Gita | Finance |
| 104 | Hari | Sales |
| 105 | Bala | HR |

Notice that Arun and Bala appear in both tables.

This will help us understand duplicate handling.

---

# 4. UNION Operator

## What is UNION?

UNION combines the result sets of multiple SELECT statements and removes duplicate rows.

### Syntax

```sql
SELECT column1
FROM table1

UNION

SELECT column1
FROM table2;
```

### Example 1 – Combine employee names

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee;
```

### Expected output

| EmployeeName |
|---|
| Arun |
| Bala |
| Charan |
| Deepa |
| Esha |
| Farhan |
| Gita |
| Hari |

### Understand the output

Arun appears in both tables.

Bala also appears in both tables.

However, UNION returns each duplicate name only once.

**Important:** UNION removes duplicate rows from the combined result.

---

# 5. UNION ALL Operator

## What is UNION ALL?

UNION ALL combines result sets from multiple SELECT statements without removing duplicates.

### Syntax

```sql
SELECT column1
FROM table1

UNION ALL

SELECT column1
FROM table2;
```

### Example 1 – Combine employee names

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

### Expected output

| EmployeeName |
|---|
| Arun |
| Bala |
| Charan |
| Deepa |
| Esha |
| Arun |
| Farhan |
| Gita |
| Hari |
| Bala |

### Understand the output

- Arun appears twice.
- Bala appears twice.
- Other names appear once.

UNION ALL retains all rows, including duplicates.

**Important:** UNION ALL does not perform duplicate elimination.

---

# 6. Difference Between UNION and UNION ALL

| Feature | UNION | UNION ALL |
|---|---|---|
| Combines result sets | Yes | Yes |
| Removes duplicate rows | Yes | No |
| Retains duplicate rows | No | Yes |
| Duplicate elimination | Required | Not required |
| Performance | Generally slower | Generally faster |

## Example – Compare row counts

### UNION

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee;
```

Expected rows: 8.

### UNION ALL

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

Expected rows: 10.

### Why is UNION generally slower?

UNION must identify and eliminate duplicate rows.

SQL Server may use operations such as sorting and hashing to perform duplicate elimination.

UNION ALL does not need to remove duplicates, so it generally requires less processing.

**Performance recommendation:** Use UNION ALL when duplicates are allowed or when you know the result sets do not contain duplicates. Use UNION when duplicate elimination is required.

---

# 7. Requirements for UNION and UNION ALL

There are important rules when combining SELECT statements.

## Rule 1 – Same number of columns

Both SELECT statements must return the same number of columns.

### Correct example

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeId, EmployeeName
FROM tblFormerEmployee;
```

### Incorrect example

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeId
FROM tblFormerEmployee;
```

The second SELECT returns only one column, while the first returns two.

SQL Server will raise an error.

## Rule 2 – Compatible data types

Corresponding columns must have compatible data types.

### Correct example

```sql
SELECT EmployeeId
FROM tblEmployee

UNION

SELECT EmployeeId
FROM tblFormerEmployee;
```

Both columns are INT.

### Example with compatible different types

```sql
SELECT CAST(EmployeeId AS VARCHAR(20)) AS EmployeeValue
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee;
```

Both expressions return compatible character data types.

SQL Server applies data type precedence and implicit conversion rules when combining compatible types.

## Rule 3 – Same column order

The columns are combined by their position, not by their names.

### Incorrect logical alignment

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName, EmployeeId
FROM tblFormerEmployee;
```

The first column of one query is combined with the first column of the other query.

The second column is combined with the second column.

Column names do not control alignment.

### Correct example

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeId, EmployeeName
FROM tblFormerEmployee;
```

## Rule 4 – Column names in the final result

The final result uses the column names from the first SELECT statement.

Example:

```sql
SELECT EmployeeName AS Name
FROM tblEmployee

UNION

SELECT EmployeeName AS FormerName
FROM tblFormerEmployee;
```

The output column name is `Name`, not `FormerName`.

---

# 8. UNION with Multiple SELECT Statements

UNION can combine results from more than two SELECT statements.

### Example

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee

UNION

SELECT 'Management' AS EmployeeName;
```

The result combines all three result sets and removes duplicate rows.

### UNION ALL example

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee

UNION ALL

SELECT 'Management' AS EmployeeName;
```

This retains all rows, including duplicates.

---

# 9. ORDER BY with UNION and UNION ALL

When combining multiple SELECT statements, ORDER BY should be placed at the end of the complete query.

### Example 1 – UNION with ORDER BY

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee

ORDER BY EmployeeName ASC;
```

### Example 2 – UNION ALL with ORDER BY

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee

ORDER BY EmployeeName DESC;
```

### Example 3 – ORDER BY using output column alias

```sql
SELECT EmployeeName AS Name
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee

ORDER BY Name;
```

### Important rules

1. ORDER BY is applied to the combined result.
2. Place ORDER BY after the final SELECT statement.
3. Use output column names from the first SELECT or valid output column positions.
4. An ORDER BY inside an individual SELECT generally requires a construct such as TOP or OFFSET/FETCH and does not guarantee the final combined ordering.

---

# 10. UNION vs JOIN

UNION and JOIN are different operations.

## UNION – Combines rows vertically

UNION combines rows from multiple result sets.

Example:

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

Conceptually:

| EmployeeName |
|---|
| Arun |
| Bala |
| Charan |
| Deepa |
| Esha |
| Arun |
| Farhan |
| Gita |
| Hari |
| Bala |

## JOIN – Combines columns horizontally

JOIN combines related columns from multiple tables using a join condition.

Let's create a department table.

```sql
CREATE TABLE tblDepartment
(
    DepartmentId INT PRIMARY KEY,
    DepartmentName VARCHAR(50)
);

INSERT INTO tblDepartment
VALUES
    (1, 'IT'),
    (2, 'HR'),
    (3, 'Finance'),
    (4, 'Sales');
```

For this JOIN example, we will use a separate employee table with DepartmentId.

```sql
CREATE TABLE tblEmployeeDepartment
(
    EmployeeId INT,
    EmployeeName VARCHAR(50),
    DepartmentId INT
);

INSERT INTO tblEmployeeDepartment
VALUES
    (1, 'Arun', 1),
    (2, 'Bala', 2),
    (3, 'Charan', 3),
    (4, 'Deepa', 1);
```

### JOIN example

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployeeDepartment E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### Expected output

| EmployeeName | DepartmentName |
|---|---|
| Arun | IT |
| Bala | HR |
| Charan | Finance |
| Deepa | IT |

### Comparison

| Feature | UNION | JOIN |
|---|---|---|
| Purpose | Combines result sets | Combines related data |
| Result shape | Adds rows | Adds columns |
| Matching condition | Not required | Usually uses a join condition |
| Column requirements | Same number of compatible columns | Can have different columns |
| Duplicate handling | UNION removes duplicates; UNION ALL retains them | Depends on the join and source data |

**Remember:**

- UNION = combines rows vertically.
- JOIN = combines columns horizontally.

---

# 11. Real-World Scenarios

## Scenario 1 – Current and former employees

Requirement: Generate a single list of current and former employees.

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

UNION ALL is suitable if we want to preserve every source record.

## Scenario 2 – Unique customer list

Requirement: Combine online and offline customers into one unique list.

```sql
SELECT CustomerName
FROM tblOnlineCustomer

UNION

SELECT CustomerName
FROM tblOfflineCustomer;
```

UNION removes duplicate result rows.

This assumes the customer tables contain the specified CustomerName column.

## Scenario 3 – Combining data from different regions

Requirement: Combine employee records from two regional tables.

```sql
SELECT EmployeeId, EmployeeName, Department
FROM tblEmployeeNorth

UNION ALL

SELECT EmployeeId, EmployeeName, Department
FROM tblEmployeeSouth;
```

UNION ALL is suitable when both regional datasets should be retained.

These examples assume the named regional tables have compatible column structures.

---

# 12. Hands-on Practice Exercises

Try writing the queries before checking the answers.

## Beginner Level

### Exercise 1
Combine EmployeeName from tblEmployee and tblFormerEmployee using UNION.

### Exercise 2
Combine EmployeeName from both tables using UNION ALL.

### Exercise 3
Count the number of rows returned by UNION.

### Exercise 4
Count the number of rows returned by UNION ALL.

### Exercise 5
Combine EmployeeId and EmployeeName from both tables using UNION.

### Exercise 6
Combine EmployeeId and EmployeeName using UNION ALL.

---

## Intermediate Level

### Exercise 7
Combine employee names from both tables and sort them in ascending order.

### Exercise 8
Combine employee names from both tables and sort them in descending order.

### Exercise 9
Combine employee names from three SELECT statements using UNION.

### Exercise 10
Combine employee names from three SELECT statements using UNION ALL.

### Exercise 11
Display employee names and Department values from both tables using UNION.

### Exercise 12
Use UNION ALL to combine employee records and retain duplicate rows.

---

## Real-World Practice

### Exercise 13
A company maintains current and former employee tables. Generate a combined list of all employee names while retaining duplicates.

### Exercise 14
A company wants a unique employee name list from current and former employee tables.

### Exercise 15
Combine employee names from both tables and sort the final result alphabetically.

### Exercise 16
Combine EmployeeId and EmployeeName from both tables. Ensure that the columns are in the correct order.

### Exercise 17
Explain why the following query produces an error:

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeId
FROM tblFormerEmployee;
```

Write the corrected query.

### Exercise 18
Explain the difference between UNION and JOIN with a SQL example.

---

# 13. Complete Answer Key

## Answer 1 – UNION

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee;
```

Expected unique names: 8 rows.

## Answer 2 – UNION ALL

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

Expected rows: 10.

## Answer 3 – Count UNION rows

```sql
SELECT COUNT(*) AS TotalRows
FROM
(
    SELECT EmployeeName
    FROM tblEmployee

    UNION

    SELECT EmployeeName
    FROM tblFormerEmployee
) AS CombinedEmployees;
```

Expected result: 8.

## Answer 4 – Count UNION ALL rows

```sql
SELECT COUNT(*) AS TotalRows
FROM
(
    SELECT EmployeeName
    FROM tblEmployee

    UNION ALL

    SELECT EmployeeName
    FROM tblFormerEmployee
) AS CombinedEmployees;
```

Expected result: 10.

## Answer 5 – UNION with two columns

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeId, EmployeeName
FROM tblFormerEmployee;
```

Expected rows: 10.

Although Arun and Bala appear in both tables, their EmployeeId values are different. Therefore, the complete rows are not duplicates.

## Answer 6 – UNION ALL with two columns

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeId, EmployeeName
FROM tblFormerEmployee;
```

Expected rows: 10.

## Answer 7 – Sort ascending

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee

ORDER BY EmployeeName ASC;
```

## Answer 8 – Sort descending

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee

ORDER BY EmployeeName DESC;
```

## Answer 9 – UNION with three SELECT statements

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee

UNION

SELECT 'Management' AS EmployeeName;
```

## Answer 10 – UNION ALL with three SELECT statements

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee

UNION ALL

SELECT 'Management' AS EmployeeName;
```

## Answer 11 – UNION with employee and department

```sql
SELECT EmployeeName, Department
FROM tblEmployee

UNION

SELECT EmployeeName, Department
FROM tblFormerEmployee;
```

Expected rows: 10.

The duplicate names Arun and Bala have matching departments, but the full rows include different EmployeeId values only if EmployeeId is selected. Here, EmployeeId is not selected, so the duplicate name-department combinations are removed.

## Answer 12 – UNION ALL with employee and department

```sql
SELECT EmployeeName, Department
FROM tblEmployee

UNION ALL

SELECT EmployeeName, Department
FROM tblFormerEmployee;
```

Expected rows: 10.

## Answer 13 – Retain all current and former employee names

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

## Answer 14 – Unique employee names

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee;
```

## Answer 15 – Alphabetically sorted unique names

```sql
SELECT EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeName
FROM tblFormerEmployee

ORDER BY EmployeeName ASC;
```

## Answer 16 – Combine employee ID and name

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeId, EmployeeName
FROM tblFormerEmployee;
```

## Answer 17 – Correct the column mismatch

Problem:

The first SELECT returns two columns, but the second SELECT returns only one.

Correct query:

```sql
SELECT EmployeeId, EmployeeName
FROM tblEmployee

UNION

SELECT EmployeeId, EmployeeName
FROM tblFormerEmployee;
```

Both SELECT statements now return two compatible columns in the same order.

## Answer 18 – UNION vs JOIN

UNION combines rows from multiple SELECT statements.

```sql
SELECT EmployeeName
FROM tblEmployee

UNION ALL

SELECT EmployeeName
FROM tblFormerEmployee;
```

JOIN combines columns from related tables.

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployeeDepartment E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

UNION stacks result sets vertically, whereas JOIN combines related columns horizontally.

---

# 14. Important SQL Rules to Remember

1. UNION combines result sets.
2. UNION removes duplicate complete rows.
3. UNION ALL retains duplicate rows.
4. Both operators require the same number of columns.
5. Corresponding columns must have compatible data types.
6. Column positions must match logically.
7. Final output column names come from the first SELECT.
8. ORDER BY belongs at the end of the combined query.
9. UNION generally requires additional work to eliminate duplicates.
10. UNION ALL is generally faster when duplicate elimination is unnecessary.
11. UNION does not require a primary key or foreign key relationship.
12. JOIN combines related columns, whereas UNION combines rows.

---

# 15. Revision Questions

1. What is UNION in SQL Server?
2. What is UNION ALL?
3. What is the difference between UNION and UNION ALL?
4. Why is UNION generally slower?
5. What happens when duplicate rows are present?
6. What are the requirements for UNION?
7. Can UNION combine three SELECT statements?
8. Where should ORDER BY be placed?
9. Which SELECT statement determines the final output column names?
10. What is the difference between UNION and JOIN?
11. Does UNION require a relationship between the tables?
12. Does UNION remove duplicates based on one column or the complete selected row?

---

# 16. Completion Checklist

- [ ] I understand UNION.
- [ ] I understand UNION ALL.
- [ ] I can identify duplicate rows.
- [ ] I understand the difference between UNION and UNION ALL.
- [ ] I understand the column compatibility requirements.
- [ ] I can combine more than two SELECT statements.
- [ ] I can sort the final result.
- [ ] I understand the performance difference.
- [ ] I understand UNION vs JOIN.
- [ ] I completed all 18 exercises.

---

# 17. Microsoft Documentation

- [UNION (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/set-operators-union-transact-sql)
- [SELECT (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql)
- [Joins (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/performance/joins)

---

# Final Practice Recommendation

1. Create both employee tables.
2. Execute UNION and observe the output.
3. Execute UNION ALL and compare the row count.
4. Include EmployeeId and EmployeeName together.
5. Observe how duplicate elimination applies to the complete selected row.
6. Practise ORDER BY with the combined result.
7. Compare UNION with JOIN.
8. Complete all 18 exercises.

**Key takeaway:** UNION combines result sets and removes duplicate complete rows. UNION ALL combines result sets and retains every row.
