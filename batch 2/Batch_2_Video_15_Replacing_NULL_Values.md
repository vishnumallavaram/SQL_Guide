
# Batch 2 – Video 15: Replacing NULL Values in SQL Server

## 1. Learning Objectives

By the end of this practice, you will understand:

- Why SQL Server returns NULL values in query results.
- How to replace NULL using ISNULL().
- How to replace NULL using COALESCE().
- How to replace NULL using CASE.
- The differences between ISNULL(), COALESCE(), and CASE.
- How to apply these methods in real-world SQL queries.

---

## 2. Understanding NULL Values

NULL represents a missing or unknown value in SQL Server.

Consider an employee table where every employee has a ManagerId.

Some employees may not have a manager assigned.

When we use a LEFT JOIN to retrieve employee and manager names, SQL Server returns NULL when no matching manager is found.

### Sample table: tblEmployee

| EmployeeId | EmployeeName | ManagerId |
|---:|---|---:|
| 1 | Arun | NULL |
| 2 | Bala | 1 |
| 3 | Charan | 1 |
| 4 | Deepa | 2 |
| 5 | Esha | 2 |
| 6 | Farhan | 3 |

### Query without NULL replacement

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

### Expected output

| EmployeeName | ManagerName |
|---|---|
| Arun | NULL |
| Bala | Arun |
| Charan | Arun |
| Deepa | Bala |
| Esha | Bala |
| Farhan | Charan |

**Problem:** Arun does not have a manager, so the ManagerName column displays NULL.

We can replace this NULL with a meaningful value such as `No manager`.

SQL Server provides different ways to achieve this.

In this lesson, we will learn three methods:

1. ISNULL()
2. COALESCE()
3. CASE statement

---

# 3. Method 1 – ISNULL() Function

## What is ISNULL()?

ISNULL() is a SQL Server function used to replace a NULL value with a specified replacement value.

### Syntax

```sql
ISNULL(check_expression, replacement_value)
```

| Parameter | Description |
|---|---|
| check_expression | Expression to check for NULL |
| replacement_value | Value returned when the expression is NULL |

### Example 1 – Replace NULL with text

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    ISNULL(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

### Expected output

| EmployeeName | ManagerName |
|---|---|
| Arun | No manager |
| Bala | Arun |
| Charan | Arun |
| Deepa | Bala |
| Esha | Bala |
| Farhan | Charan |

### Understanding the result

- If ManagerName contains Arun, return Arun.
- If ManagerName contains Bala, return Bala.
- If ManagerName contains NULL, return No manager.

### Example 2 – Replace NULL with zero

Consider a salary table.

```sql
CREATE TABLE tblEmployeeSalary
(
    EmployeeId INT,
    EmployeeName VARCHAR(50),
    Salary DECIMAL(10,2)
);

INSERT INTO tblEmployeeSalary
VALUES
    (1, 'Arun', 50000),
    (2, 'Bala', NULL),
    (3, 'Charan', 60000),
    (4, 'Deepa', NULL);
```

Query:

```sql
SELECT
    EmployeeName,
    Salary,
    ISNULL(Salary, 0) AS SalaryAfterReplacement
FROM tblEmployeeSalary;
```

Expected output:

| EmployeeName | Salary | SalaryAfterReplacement |
|---|---:|---:|
| Arun | 50000 | 50000 |
| Bala | NULL | 0 |
| Charan | 60000 | 60000 |
| Deepa | NULL | 0 |

### Example 3 – Replace NULL in a calculation

```sql
SELECT
    EmployeeName,
    ISNULL(Salary, 0) + 5000 AS UpdatedSalary
FROM tblEmployeeSalary;
```

Expected output:

| EmployeeName | UpdatedSalary |
|---|---:|
| Arun | 55000 |
| Bala | 5000 |
| Charan | 65000 |
| Deepa | 5000 |

**Important:** ISNULL() replaces NULL in the query result. It does not update the original table.

---

# 4. Method 2 – COALESCE() Function

## What is COALESCE()?

COALESCE() returns the first non-NULL expression from a list of expressions.

It is more flexible than ISNULL() because it can accept multiple expressions.

### Syntax

```sql
COALESCE(expression1, expression2, expression3, ...);
```

### Example 1 – Basic COALESCE

```sql
SELECT
    COALESCE(NULL, 'SQL', 'Python', 'Java') AS Result;
```

Expected output:

| Result |
|---|
| SQL |

Explanation:

- First expression: NULL.
- Second expression: SQL.
- SQL is the first non-NULL value.
- SQL Server returns SQL.

### Example 2 – Replace NULL manager name

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    COALESCE(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

Expected output:

| EmployeeName | ManagerName |
|---|---|
| Arun | No manager |
| Bala | Arun |
| Charan | Arun |
| Deepa | Bala |
| Esha | Bala |
| Farhan | Charan |

### Example 3 – Multiple fallback columns

Consider a customer table.

```sql
CREATE TABLE tblCustomer
(
    CustomerId INT,
    CustomerName VARCHAR(50),
    Email VARCHAR(100),
    Phone VARCHAR(20),
    AlternatePhone VARCHAR(20)
);

INSERT INTO tblCustomer
VALUES
    (1, 'Arun', 'arun@email.com', '9876543210', '9123456780'),
    (2, 'Bala', NULL, '9876500000', '9123400000'),
    (3, 'Charan', NULL, NULL, '9123456789'),
    (4, 'Deepa', NULL, NULL, NULL);
```

Query:

```sql
SELECT
    CustomerName,
    COALESCE(Email, Phone, AlternatePhone, 'Not Available')
        AS ContactDetails
FROM tblCustomer;
```

Expected output:

| CustomerName | ContactDetails |
|---|---|
| Arun | arun@email.com |
| Bala | 9876500000 |
| Charan | 9123456789 |
| Deepa | Not Available |

Explanation:

COALESCE checks the expressions from left to right.

It returns the first non-NULL value.

This makes it useful when multiple fallback values are available.

---

# 5. Method 3 – CASE Statement

## What is CASE?

CASE is a conditional expression in SQL Server.

It evaluates conditions and returns a value based on the result.

### Syntax

```sql
CASE
    WHEN condition THEN result
    ELSE alternative_result
END
```

### Example 1 – Basic CASE

```sql
SELECT
    CASE
        WHEN NULL IS NULL THEN 'No manager'
        ELSE 'Manager available'
    END AS ManagerStatus;
```

Expected output:

| ManagerStatus |
|---|
| No manager |

### Example 2 – Replace NULL manager name

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    CASE
        WHEN M.EmployeeName IS NULL THEN 'No manager'
        ELSE M.EmployeeName
    END AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

Expected output:

| EmployeeName | ManagerName |
|---|---|
| Arun | No manager |
| Bala | Arun |
| Charan | Arun |
| Deepa | Bala |
| Esha | Bala |
| Farhan | Charan |

### Understanding the CASE logic

1. Check whether M.EmployeeName IS NULL.
2. If TRUE, return No manager.
3. Otherwise, return the actual manager name.

### Example 3 – Categorize salary

```sql
SELECT
    EmployeeName,
    Salary,
    CASE
        WHEN Salary IS NULL THEN 'Salary Not Available'
        WHEN Salary >= 60000 THEN 'High Salary'
        WHEN Salary >= 50000 THEN 'Medium Salary'
        ELSE 'Low Salary'
    END AS SalaryCategory
FROM tblEmployeeSalary;
```

Expected output:

| EmployeeName | Salary | SalaryCategory |
|---|---:|---|
| Arun | 50000 | Medium Salary |
| Bala | NULL | Salary Not Available |
| Charan | 60000 | High Salary |
| Deepa | NULL | Salary Not Available |

**Important:** CASE is not limited to replacing NULL. It can evaluate multiple conditions and return different results.

---

# 6. Comparison – ISNULL(), COALESCE(), CASE

| Feature | ISNULL() | COALESCE() | CASE |
|---|---|---|---|
| Purpose | Replace NULL | Return first non-NULL expression | Evaluate conditions |
| Arguments | Two | Two or more | One or more WHEN branches |
| Multiple fallback values | No | Yes | Yes, using conditions |
| SQL Server support | Yes | Yes | Yes |
| Can handle other conditions | No | No | Yes |

## SQL Server-specific differences

### 1. Return data type

ISNULL() generally returns the data type of its first argument. The replacement value is converted to that type.

COALESCE() follows SQL Server data type precedence rules to determine the result type.

Example:

```sql
SELECT
    ISNULL(CAST(NULL AS VARCHAR(5)), 'No manager')
        AS IsnullResult;
```

The result type is VARCHAR(5). A longer replacement string can be truncated.

```sql
SELECT
    COALESCE(CAST(NULL AS VARCHAR(5)), 'No manager')
        AS CoalesceResult;
```

COALESCE determines its result type using data type precedence and the types of its expressions. In this example, the result can accommodate the longer string.

### 2. Number of expressions

ISNULL:

```sql
SELECT ISNULL(NULL, 'Default Value') AS Result;
```

COALESCE:

```sql
SELECT
    COALESCE(NULL, NULL, 'Default Value') AS Result;
```

### 3. Conditional logic

ISNULL and COALESCE replace NULL values.

CASE can evaluate more complex conditions.

Example:

```sql
SELECT
    EmployeeName,
    CASE
        WHEN Salary IS NULL THEN 'Not Available'
        WHEN Salary >= 60000 THEN 'High'
        ELSE 'Normal'
    END AS SalaryStatus
FROM tblEmployeeSalary;
```

---

# 7. Same Requirement – Three Different Methods

Requirement:

Display all employees and their manager names. If an employee does not have a manager, display `No manager`.

## Method 1 – ISNULL()

```sql
SELECT
    E.EmployeeName,
    ISNULL(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Method 2 – COALESCE()

```sql
SELECT
    E.EmployeeName,
    COALESCE(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Method 3 – CASE

```sql
SELECT
    E.EmployeeName,
    CASE
        WHEN M.EmployeeName IS NULL THEN 'No manager'
        ELSE M.EmployeeName
    END AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

All three queries produce the same output for this requirement.

---

# 8. Important NULL Rules

### Rule 1 – Do not use = NULL

Incorrect:

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId = NULL;
```

Correct:

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId IS NULL;
```

### Rule 2 – Use IS NOT NULL

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId IS NOT NULL;
```

### Rule 3 – NULL is not the same as an empty string

NULL means missing or unknown.

An empty string (`''`) is a string value containing zero characters.

### Rule 4 – NULL replacement does not change stored data

```sql
SELECT
    ISNULL(ManagerId, 0) AS ManagerId
FROM tblEmployee;
```

This changes only the displayed result. It does not update ManagerId in the table.

---

# 9. Hands-on Practice Exercises

Try to solve these exercises before checking the answers.

## Beginner Level

### Exercise 1
Display employee names and manager names using ISNULL(). Replace NULL with `No manager`.

### Exercise 2
Display employee names and manager names using COALESCE().

### Exercise 3
Display employee names and manager names using CASE.

### Exercise 4
Display all employees whose ManagerId is NULL.

### Exercise 5
Display all employees whose ManagerId is NOT NULL.

### Exercise 6
Replace NULL salary values with zero using ISNULL().

---

## Intermediate Level

### Exercise 7
Use COALESCE() to return the first available value from Email, Phone, and AlternatePhone.

### Exercise 8
Use CASE to display `Salary Not Available` when salary is NULL.

### Exercise 9
Use CASE to categorize salaries:
- NULL: Not Available
- 60000 or above: High
- 50000 or above: Medium
- Below 50000: Low

### Exercise 10
Display employee and manager names using ISNULL(), but show `Manager Not Assigned` instead of `No manager`.

### Exercise 11
Use COALESCE() with three NULL expressions and a default value of `Unknown`.

### Exercise 12
Display customer contact details using COALESCE(). If all contact columns are NULL, return `Contact Not Available`.

---

## Real-World Practice

### Exercise 13
An employee report should display:
- Employee name
- Manager name
- Manager status

Use CASE to display `Assigned` or `Not Assigned`.

### Exercise 14
A payroll report should display employee name and salary. Replace NULL salary with zero and calculate an additional bonus of 5000.

### Exercise 15
A customer report has three contact fields: Email, Phone, and AlternatePhone. Display the first available contact value.

### Exercise 16
Display employee name and manager name. If manager name is NULL, display `Top Level Employee`.

### Exercise 17
Write the same employee-manager query using all three methods: ISNULL(), COALESCE(), and CASE.

### Exercise 18
Explain why `ManagerId = NULL` does not correctly retrieve employees without managers. Write the correct query.

---

# 10. Complete Answer Key

## Answer 1 – ISNULL()

```sql
SELECT
    E.EmployeeName,
    ISNULL(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 2 – COALESCE()

```sql
SELECT
    E.EmployeeName,
    COALESCE(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 3 – CASE

```sql
SELECT
    E.EmployeeName,
    CASE
        WHEN M.EmployeeName IS NULL THEN 'No manager'
        ELSE M.EmployeeName
    END AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 4 – Employees without a manager

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId IS NULL;
```

Expected employee: Arun.

## Answer 5 – Employees with an assigned manager

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId IS NOT NULL;
```

Expected employees: Bala, Charan, Deepa, Esha, Farhan.

## Answer 6 – Replace NULL salary with zero

```sql
SELECT
    EmployeeName,
    ISNULL(Salary, 0) AS Salary
FROM tblEmployeeSalary;
```

## Answer 7 – First available customer contact

```sql
SELECT
    CustomerName,
    COALESCE(Email, Phone, AlternatePhone) AS ContactDetails
FROM tblCustomer;
```

## Answer 8 – Salary availability using CASE

```sql
SELECT
    EmployeeName,
    CASE
        WHEN Salary IS NULL THEN 'Salary Not Available'
        ELSE 'Salary Available'
    END AS SalaryStatus
FROM tblEmployeeSalary;
```

## Answer 9 – Salary categorization

```sql
SELECT
    EmployeeName,
    Salary,
    CASE
        WHEN Salary IS NULL THEN 'Not Available'
        WHEN Salary >= 60000 THEN 'High'
        WHEN Salary >= 50000 THEN 'Medium'
        ELSE 'Low'
    END AS SalaryCategory
FROM tblEmployeeSalary;
```

## Answer 10 – Custom manager placeholder

```sql
SELECT
    E.EmployeeName,
    ISNULL(M.EmployeeName, 'Manager Not Assigned') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 11 – Multiple NULL expressions

```sql
SELECT
    COALESCE(NULL, NULL, NULL, 'Unknown') AS Result;
```

Expected output: Unknown.

## Answer 12 – Customer contact details

```sql
SELECT
    CustomerName,
    COALESCE(
        Email,
        Phone,
        AlternatePhone,
        'Contact Not Available'
    ) AS ContactDetails
FROM tblCustomer;
```

## Answer 13 – Employee manager status

```sql
SELECT
    E.EmployeeName,
    M.EmployeeName AS ManagerName,
    CASE
        WHEN M.EmployeeName IS NULL THEN 'Not Assigned'
        ELSE 'Assigned'
    END AS ManagerStatus
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 14 – Payroll report with bonus

```sql
SELECT
    EmployeeName,
    ISNULL(Salary, 0) AS Salary,
    ISNULL(Salary, 0) + 5000 AS SalaryWithBonus
FROM tblEmployeeSalary;
```

## Answer 15 – First available contact

```sql
SELECT
    CustomerName,
    COALESCE(
        Email,
        Phone,
        AlternatePhone,
        'No Contact Available'
    ) AS ContactDetails
FROM tblCustomer;
```

## Answer 16 – Top-level employee

```sql
SELECT
    E.EmployeeName,
    ISNULL(M.EmployeeName, 'Top Level Employee') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 17 – Three methods

**ISNULL():**

```sql
SELECT
    E.EmployeeName,
    ISNULL(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

**COALESCE():**

```sql
SELECT
    E.EmployeeName,
    COALESCE(M.EmployeeName, 'No manager') AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

**CASE:**

```sql
SELECT
    E.EmployeeName,
    CASE
        WHEN M.EmployeeName IS NULL THEN 'No manager'
        ELSE M.EmployeeName
    END AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 18 – NULL comparison

Incorrect:

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId = NULL;
```

Correct:

```sql
SELECT *
FROM tblEmployee
WHERE ManagerId IS NULL;
```

Explanation:

SQL Server uses three-valued logic: TRUE, FALSE, and UNKNOWN. Comparing a value with NULL using `=` results in UNKNOWN, not TRUE. Therefore, use IS NULL to find NULL values.

---

# 11. Revision Questions

1. What is NULL in SQL Server?
2. Why does a LEFT JOIN return NULL values?
3. What is the purpose of ISNULL()?
4. What is the syntax of ISNULL()?
5. What is the difference between ISNULL() and COALESCE()?
6. Can COALESCE() accept more than two expressions?
7. What does COALESCE() return?
8. What is the purpose of a CASE statement?
9. Can CASE evaluate multiple conditions?
10. Why should we use IS NULL instead of = NULL?
11. Does ISNULL() update the original table?
12. Which method would you use when multiple fallback values are available?

---

# 12. Completion Checklist

- [ ] I understand NULL values.
- [ ] I understand why LEFT JOIN returns NULL.
- [ ] I can replace NULL using ISNULL().
- [ ] I can replace NULL using COALESCE().
- [ ] I can replace NULL using CASE.
- [ ] I understand the differences between all three methods.
- [ ] I understand IS NULL and IS NOT NULL.
- [ ] I can use COALESCE() with multiple expressions.
- [ ] I can write employee-manager queries using all three methods.
- [ ] I completed all 18 exercises.

---

# 13. Microsoft Documentation

- [ISNULL (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/functions/isnull-transact-sql)
- [COALESCE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/coalesce-transact-sql)
- [CASE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql)
- [NULL and UNKNOWN (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/null-and-unknown-transact-sql)

---

## Final Practice Recommendation

1. Execute the employee-manager LEFT JOIN without NULL replacement.
2. Execute the same query using ISNULL().
3. Execute it again using COALESCE().
4. Execute it again using CASE.
5. Compare the outputs.
6. Complete all 18 exercises without referring to the solutions.

**Key takeaway:** ISNULL() replaces a NULL value, COALESCE() finds the first non-NULL expression, and CASE provides conditional logic.
