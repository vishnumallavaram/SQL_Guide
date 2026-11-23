
# Batch 2 – Video 16: COALESCE() Function in SQL Server

## 1. Learning Objectives

By the end of this practice, you will understand:

- What COALESCE() does.
- Why COALESCE() is useful for handling NULL values.
- How SQL Server evaluates expressions inside COALESCE().
- How to return the first non-NULL value.
- How column order affects the result.
- How to use COALESCE() with multiple columns.
- How to apply COALESCE() in practical SQL queries.

---

## 2. Introduction to COALESCE()

COALESCE() is a SQL Server function that returns the first non-NULL value from a list of expressions.

It evaluates expressions from left to right.

When it finds the first non-NULL value, it returns that value.

### Syntax

```sql
COALESCE(expression1, expression2, expression3, ...);
```

### Important points

1. COALESCE() accepts multiple expressions.
2. Expressions are evaluated from left to right.
3. It returns the first non-NULL expression.
4. If every expression is NULL, it returns NULL.
5. The order of expressions determines the result.

---

## 3. Understanding How COALESCE() Works

Consider the following example:

```sql
SELECT COALESCE(NULL, 'SQL', 'Python', 'Java') AS Result;
```

### Evaluation

| Expression | Value | Action |
|---|---|---|
| 1 | NULL | Move to next expression |
| 2 | SQL | First non-NULL value found |
| 3 | Python | Not needed |
| 4 | Java | Not needed |

### Expected output

| Result |
|---|
| SQL |

COALESCE() returns SQL because it is the first non-NULL expression.

### Example 2

```sql
SELECT COALESCE(NULL, NULL, 'Python', 'Java') AS Result;
```

Expected output:

| Result |
|---|
| Python |

### Example 3

```sql
SELECT COALESCE(NULL, NULL, NULL) AS Result;
```

Expected output:

| Result |
|---|
| NULL |

**Remember:** If all expressions are NULL, COALESCE() returns NULL.

---

# 4. Create the Sample Employee Table

In this lesson, we will use employee name columns.

An employee may have:

- FirstName
- MiddleName
- LastName

Some employees may not have values in every column.

### Create table

```sql
CREATE TABLE tblEmployeeName
(
    EmployeeId INT PRIMARY KEY,
    FirstName VARCHAR(50),
    MiddleName VARCHAR(50),
    LastName VARCHAR(50)
);
```

### Insert sample records

```sql
INSERT INTO tblEmployeeName
    (EmployeeId, FirstName, MiddleName, LastName)
VALUES
    (1, 'Arun', 'Kumar', 'Reddy'),
    (2, NULL, 'Bala', 'Krishna'),
    (3, NULL, NULL, 'Charan'),
    (4, 'Deepa', NULL, 'Reddy'),
    (5, NULL, 'Esha', NULL),
    (6, NULL, NULL, NULL),
    (7, 'Farhan', 'Ali', NULL),
    (8, 'Gita', NULL, NULL);
```

### View the records

```sql
SELECT *
FROM tblEmployeeName;
```

### Expected output

| EmployeeId | FirstName | MiddleName | LastName |
|---:|---|---|---|
| 1 | Arun | Kumar | Reddy |
| 2 | NULL | Bala | Krishna |
| 3 | NULL | NULL | Charan |
| 4 | Deepa | NULL | Reddy |
| 5 | NULL | Esha | NULL |
| 6 | NULL | NULL | NULL |
| 7 | Farhan | Ali | NULL |
| 8 | Gita | NULL | NULL |

---

# 5. Practical Example – COALESCE(FirstName, MiddleName, LastName)

### Requirement

Display the first available name for every employee.

Priority:

1. FirstName
2. MiddleName
3. LastName

### Query

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, MiddleName, LastName) AS AvailableName
FROM tblEmployeeName;
```

### Expected output

| EmployeeId | AvailableName |
|---:|---|
| 1 | Arun |
| 2 | Bala |
| 3 | Charan |
| 4 | Deepa |
| 5 | Esha |
| 6 | NULL |
| 7 | Farhan |
| 8 | Gita |

### Understand the output

| EmployeeId | FirstName | MiddleName | LastName | AvailableName |
|---:|---|---|---|---|
| 1 | Arun | Kumar | Reddy | Arun |
| 2 | NULL | Bala | Krishna | Bala |
| 3 | NULL | NULL | Charan | Charan |
| 4 | Deepa | NULL | Reddy | Deepa |
| 5 | NULL | Esha | NULL | Esha |
| 6 | NULL | NULL | NULL | NULL |
| 7 | Farhan | Ali | NULL | Farhan |
| 8 | Gita | NULL | NULL | Gita |

### Key observations

- Employee 1: FirstName is available, so Arun is returned.
- Employee 2: FirstName is NULL, so Bala is returned.
- Employee 3: FirstName and MiddleName are NULL, so Charan is returned.
- Employee 4: FirstName is available, so Deepa is returned.
- Employee 5: FirstName is NULL, so Esha is returned.
- Employee 6: All three values are NULL, so the result is NULL.

---

# 6. Understanding Column Priority

The order of columns inside COALESCE() matters.

Let's change the order.

### Query 1 – FirstName has priority

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, MiddleName, LastName) AS AvailableName
FROM tblEmployeeName;
```

### Query 2 – LastName has priority

```sql
SELECT
    EmployeeId,
    COALESCE(LastName, MiddleName, FirstName) AS AvailableName
FROM tblEmployeeName;
```

### Compare the results

| EmployeeId | FirstName Priority | LastName Priority |
|---:|---|---|
| 1 | Arun | Reddy |
| 2 | Bala | Krishna |
| 3 | Charan | Charan |
| 4 | Deepa | Reddy |
| 5 | Esha | Esha |
| 6 | NULL | NULL |
| 7 | Farhan | Ali |
| 8 | Gita | Gita |

**Important:** COALESCE() does not combine values. It returns only one value based on the order of the expressions.

---

# 7. COALESCE() with Multiple Columns

COALESCE() is not limited to employee names.

It can be used whenever multiple columns can provide alternative values.

## Example 1 – Customer contact details

### Create table

```sql
CREATE TABLE tblCustomerContact
(
    CustomerId INT PRIMARY KEY,
    CustomerName VARCHAR(50),
    Email VARCHAR(100),
    Phone VARCHAR(20),
    AlternatePhone VARCHAR(20)
);
```

### Insert data

```sql
INSERT INTO tblCustomerContact
VALUES
    (1, 'Arun', 'arun@email.com', '9876543210', '9123456780'),
    (2, 'Bala', NULL, '9876500000', '9123400000'),
    (3, 'Charan', NULL, NULL, '9123456789'),
    (4, 'Deepa', NULL, NULL, NULL);
```

### Requirement

Display the first available contact detail.

Priority:

1. Email
2. Phone
3. AlternatePhone

### Query

```sql
SELECT
    CustomerId,
    CustomerName,
    COALESCE(
        Email,
        Phone,
        AlternatePhone,
        'Contact Not Available'
    ) AS ContactDetails
FROM tblCustomerContact;
```

### Expected output

| CustomerName | ContactDetails |
|---|---|
| Arun | arun@email.com |
| Bala | 9876500000 |
| Charan | 9123456789 |
| Deepa | Contact Not Available |

## Example 2 – Address selection

Suppose an employee has a permanent address and a temporary address.

Requirement: Display the temporary address if available; otherwise display the permanent address.

```sql
SELECT
    EmployeeId,
    COALESCE(TemporaryAddress, PermanentAddress) AS EmployeeAddress
FROM tblEmployeeAddress;
```

This example assumes that `tblEmployeeAddress` contains the two address columns.

## Example 3 – Default value

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, 'Name Not Available') AS EmployeeName
FROM tblEmployeeName;
```

This replaces a NULL FirstName with a default value.

---

# 8. COALESCE() with Calculations

COALESCE() can also be used in arithmetic expressions.

Consider the salary table:

```sql
CREATE TABLE tblEmployeeSalary
(
    EmployeeId INT,
    EmployeeName VARCHAR(50),
    BasicSalary DECIMAL(10,2),
    Bonus DECIMAL(10,2)
);

INSERT INTO tblEmployeeSalary
VALUES
    (1, 'Arun', 50000, 5000),
    (2, 'Bala', 45000, NULL),
    (3, 'Charan', NULL, 4000),
    (4, 'Deepa', NULL, NULL);
```

### Requirement

Calculate total salary by replacing NULL salary components with zero.

```sql
SELECT
    EmployeeName,
    COALESCE(BasicSalary, 0) AS BasicSalary,
    COALESCE(Bonus, 0) AS Bonus,
    COALESCE(BasicSalary, 0) + COALESCE(Bonus, 0)
        AS TotalSalary
FROM tblEmployeeSalary;
```

### Expected output

| EmployeeName | BasicSalary | Bonus | TotalSalary |
|---|---:|---:|---:|
| Arun | 50000 | 5000 | 55000 |
| Bala | 45000 | 0 | 45000 |
| Charan | 0 | 4000 | 4000 |
| Deepa | 0 | 0 | 0 |

**Important:** COALESCE() handles NULL values in the calculation. It does not change the stored salary values.

---

# 9. COALESCE() vs ISNULL()

Both functions can replace NULL values, but they have different capabilities.

| Feature | COALESCE() | ISNULL() |
|---|---|---|
| Number of expressions | Two or more | Exactly two |
| Returns first non-NULL expression | Yes | No, checks one expression |
| Multiple fallback values | Yes | No |
| SQL Server function | Yes | Yes |

### ISNULL example

```sql
SELECT
    ISNULL(FirstName, 'Unknown') AS EmployeeName
FROM tblEmployeeName;
```

### COALESCE example

```sql
SELECT
    COALESCE(FirstName, MiddleName, LastName, 'Unknown')
        AS EmployeeName
FROM tblEmployeeName;
```

### Important SQL Server note

COALESCE() follows SQL Server data type precedence rules to determine its result type.

ISNULL() generally returns the data type of its first argument.

Also, SQL Server can evaluate expressions inside COALESCE() more than once in certain circumstances because it is internally rewritten as a CASE expression. Be careful when using expressions such as subqueries that may return different results between evaluations.

---

# 10. Hands-on Practice Exercises

Try solving the questions before checking the answer key.

## Beginner Level

### Exercise 1
Display the first available name using FirstName, MiddleName, and LastName.

### Exercise 2
Display the first available name using LastName, MiddleName, and FirstName priority.

### Exercise 3
Display EmployeeId and FirstName. If FirstName is NULL, return `Unknown`.

### Exercise 4
Use COALESCE() to return `Not Available` when all three name columns are NULL.

### Exercise 5
Find employees whose FirstName, MiddleName, and LastName are all NULL.

### Exercise 6
Display FirstName if available; otherwise display MiddleName.

## Intermediate Level

### Exercise 7
Display the first available customer contact detail using Email, Phone, and AlternatePhone.

### Exercise 8
Display the first available employee name using COALESCE(), along with EmployeeId.

### Exercise 9
Use COALESCE() to replace NULL BasicSalary and Bonus values with zero. Calculate TotalSalary.

### Exercise 10
Display the first available name from MiddleName, FirstName, and LastName, in that priority order.

### Exercise 11
Return the first non-NULL value from the following expressions:

- NULL
- NULL
- SQL
- Python

### Exercise 12
Return `No Value` when all three expressions are NULL.

## Real-World Practice

### Exercise 13
A customer has Email, Phone, and AlternatePhone. Return the first available contact detail. If none is available, return `No Contact`.

### Exercise 14
An employee has CurrentAddress and PermanentAddress. Return CurrentAddress first, otherwise PermanentAddress, otherwise `Address Not Available`.

### Exercise 15
An employee report should show FirstName, otherwise MiddleName, otherwise LastName, otherwise `Name Missing`.

### Exercise 16
Write a query to display the first available salary component from BasicSalary, Bonus, and Allowance.

### Exercise 17
Display the first available contact value, but change the priority to AlternatePhone, Phone, and Email.

### Exercise 18
Explain what happens when all expressions passed to COALESCE() are NULL.

---

# 11. Complete Answer Key

## Answer 1 – FirstName priority

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, MiddleName, LastName) AS AvailableName
FROM tblEmployeeName;
```

## Answer 2 – LastName priority

```sql
SELECT
    EmployeeId,
    COALESCE(LastName, MiddleName, FirstName) AS AvailableName
FROM tblEmployeeName;
```

## Answer 3 – Replace NULL FirstName

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, 'Unknown') AS FirstName
FROM tblEmployeeName;
```

## Answer 4 – Default value when all names are NULL

```sql
SELECT
    EmployeeId,
    COALESCE(
        FirstName,
        MiddleName,
        LastName,
        'Not Available'
    ) AS AvailableName
FROM tblEmployeeName;
```

## Answer 5 – Find employees with all name columns NULL

```sql
SELECT *
FROM tblEmployeeName
WHERE FirstName IS NULL
  AND MiddleName IS NULL
  AND LastName IS NULL;
```

Expected employee: EmployeeId 6.

## Answer 6 – FirstName or MiddleName

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, MiddleName) AS AvailableName
FROM tblEmployeeName;
```

## Answer 7 – Customer contact details

```sql
SELECT
    CustomerName,
    COALESCE(
        Email,
        Phone,
        AlternatePhone,
        'No Contact'
    ) AS ContactDetails
FROM tblCustomerContact;
```

## Answer 8 – Employee available name

```sql
SELECT
    EmployeeId,
    COALESCE(FirstName, MiddleName, LastName) AS AvailableName
FROM tblEmployeeName;
```

## Answer 9 – Salary calculation

```sql
SELECT
    EmployeeName,
    COALESCE(BasicSalary, 0) AS BasicSalary,
    COALESCE(Bonus, 0) AS Bonus,
    COALESCE(BasicSalary, 0) + COALESCE(Bonus, 0)
        AS TotalSalary
FROM tblEmployeeSalary;
```

## Answer 10 – MiddleName priority

```sql
SELECT
    EmployeeId,
    COALESCE(MiddleName, FirstName, LastName) AS AvailableName
FROM tblEmployeeName;
```

## Answer 11 – First non-NULL expression

```sql
SELECT
    COALESCE(NULL, NULL, 'SQL', 'Python') AS Result;
```

Expected output: SQL.

## Answer 12 – All NULL expressions

```sql
SELECT
    COALESCE(NULL, NULL, NULL, 'No Value') AS Result;
```

Expected output: No Value.

## Answer 13 – Customer contact fallback

```sql
SELECT
    CustomerName,
    COALESCE(
        Email,
        Phone,
        AlternatePhone,
        'No Contact'
    ) AS ContactDetails
FROM tblCustomerContact;
```

## Answer 14 – Address fallback

```sql
SELECT
    EmployeeId,
    COALESCE(
        CurrentAddress,
        PermanentAddress,
        'Address Not Available'
    ) AS EmployeeAddress
FROM tblEmployeeAddress;
```

This assumes the address table contains these columns.

## Answer 15 – Employee name fallback

```sql
SELECT
    EmployeeId,
    COALESCE(
        FirstName,
        MiddleName,
        LastName,
        'Name Missing'
    ) AS EmployeeName
FROM tblEmployeeName;
```

## Answer 16 – First available salary component

```sql
SELECT
    EmployeeId,
    EmployeeName,
    COALESCE(BasicSalary, Bonus, Allowance, 0)
        AS AvailableSalaryComponent
FROM tblEmployeeCompensation;
```

This assumes the table contains the specified salary columns.

## Answer 17 – AlternatePhone priority

```sql
SELECT
    CustomerName,
    COALESCE(
        AlternatePhone,
        Phone,
        Email,
        'No Contact'
    ) AS ContactDetails
FROM tblCustomerContact;
```

## Answer 18 – All expressions are NULL

If all expressions are NULL, COALESCE() returns NULL.

Example:

```sql
SELECT COALESCE(NULL, NULL, NULL) AS Result;
```

Expected output:

| Result |
|---|
| NULL |

If a default value is required, include it as the final expression:

```sql
SELECT
    COALESCE(NULL, NULL, NULL, 'Unknown') AS Result;
```

Expected output: Unknown.

---

# 12. Revision Questions

1. What is COALESCE()?
2. What does COALESCE() return?
3. In which direction does COALESCE() evaluate expressions?
4. What happens when the first expression is NULL?
5. What happens when the first expression is not NULL?
6. Can COALESCE() accept more than two expressions?
7. What happens when all expressions are NULL?
8. Does COALESCE() combine values from multiple columns?
9. Does changing the expression order affect the output?
10. What is the difference between COALESCE() and ISNULL()?
11. Can COALESCE() be used in calculations?
12. Why is COALESCE() useful in real-world SQL queries?

---

# 13. Completion Checklist

- [ ] I understand the purpose of COALESCE().
- [ ] I understand how expressions are evaluated.
- [ ] I can identify the first non-NULL value.
- [ ] I can use COALESCE() with three or more columns.
- [ ] I understand how expression order changes the result.
- [ ] I can use COALESCE() with employee name columns.
- [ ] I can use COALESCE() with customer contact details.
- [ ] I can use COALESCE() in calculations.
- [ ] I understand the difference between COALESCE() and ISNULL().
- [ ] I completed all 18 exercises.

---

# 14. Microsoft Documentation

- [COALESCE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/coalesce-transact-sql)
- [ISNULL (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/functions/isnull-transact-sql)
- [NULL and UNKNOWN (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/null-and-unknown-transact-sql)

---

# Final Practice Recommendation

1. Create the employee name table.
2. Insert all eight sample records.
3. Run COALESCE(FirstName, MiddleName, LastName).
4. Change the order of the columns.
5. Compare the results.
6. Practise using COALESCE() with customer contact details.
7. Complete all 18 exercises without looking at the answers.

**Key takeaway:** COALESCE() returns the first non-NULL value from the expressions supplied, in the exact order specified.
