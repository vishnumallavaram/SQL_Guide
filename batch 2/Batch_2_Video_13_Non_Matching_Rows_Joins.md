
# Batch 2 – Video 13: Advanced JOIN Techniques – Finding Non-Matching Records

## 1. Learning Objectives

By the end of this practice session, students should be able to:

1. Understand matching and non-matching records.
2. Retrieve records present only in the left table.
3. Retrieve records present only in the right table.
4. Retrieve non-matching records from both tables.
5. Use LEFT JOIN with WHERE and IS NULL.
6. Use RIGHT JOIN with WHERE and IS NULL.
7. Use FULL OUTER JOIN with OR and IS NULL.
8. Understand why `= NULL` is incorrect.
9. Understand the difference between `IS NULL` and `IS NOT NULL`.
10. Apply JOIN techniques to real-world data reconciliation.

---

# PART 1 – UNDERSTANDING THE CONCEPT

## 2. What Are Non-Matching Records?

Consider two related tables:

**tblEmployee**

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---:|
| 101 | Arun | 1 |
| 102 | Bala | 2 |
| 103 | Charan | 1 |
| 104 | Deepa | 3 |
| 105 | Esha | 5 |
| 106 | Farhan | 2 |
| 107 | Gita | NULL |

**tblDepartment**

| DepartmentId | DepartmentName |
|---:|---|
| 1 | IT |
| 2 | HR |
| 3 | Finance |
| 4 | Sales |

Observe the data:

- Arun belongs to IT.
- Bala belongs to HR.
- Charan belongs to IT.
- Deepa belongs to Finance.
- Esha has DepartmentId 5, which does not exist in tblDepartment.
- Gita does not have a department assigned.
- Sales exists in tblDepartment but has no employees.

These are examples of matching and non-matching records.

### What do we want to identify?

| Requirement | Expected records |
|---|---|
| Records present only in Employee | Esha and Gita |
| Records present only in Department | Sales |
| Non-matching records from both tables | Esha, Gita and Sales |

This is the main objective of Video 13.

---

## 3. Practice Environment

Use the `tblEmployee` and `tblDepartment` tables created in Video 12.

If those tables already exist and contain the sample records, skip this section.

### Create tblDepartment

```sql
CREATE TABLE tblDepartment
(
    DepartmentId INT PRIMARY KEY,
    DepartmentName VARCHAR(50)
);
```

### Insert department data

```sql
INSERT INTO tblDepartment
(DepartmentId, DepartmentName)
VALUES
(1, 'IT'),
(2, 'HR'),
(3, 'Finance'),
(4, 'Sales');
```

### Create tblEmployee

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    DepartmentId INT
);
```

### Insert employee data

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

### Verify both tables

```sql
SELECT * FROM tblEmployee;

SELECT * FROM tblDepartment;
```

Important: DepartmentId in tblEmployee intentionally has no foreign key constraint. This allows us to practise invalid department IDs and unmatched records.

---

# PART 2 – NON-MATCHING RECORDS FROM THE LEFT TABLE

## 4. LEFT JOIN + WHERE IS NULL

### Concept

A LEFT JOIN returns:

- All records from the left table.
- Matching records from the right table.
- NULL values for right-table columns when no match exists.

If we add a WHERE condition checking whether the right table's primary key is NULL, we can retrieve records that exist only in the left table.

### Generic syntax

```sql
SELECT
    A.ColumnName,
    B.ColumnName
FROM TableA A
LEFT JOIN TableB B
    ON A.CommonColumn = B.CommonColumn
WHERE B.PrimaryKey IS NULL;
```

### Example 1: Find employees without matching departments

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

### Expected result

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---:|
| 105 | Esha | 5 |
| 107 | Gita | NULL |

### Explanation

1. LEFT JOIN keeps every employee.
2. Matching department information is retrieved where available.
3. Esha has DepartmentId 5, but no matching department exists.
4. Gita has no DepartmentId.
5. Both records receive NULL in the department table's columns.
6. WHERE D.DepartmentId IS NULL returns those unmatched employees.

### Example 2: Find employees with invalid department IDs only

Sometimes we want to exclude employees whose DepartmentId is genuinely NULL.

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL
  AND E.DepartmentId IS NOT NULL;
```

Expected result:

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---:|
| 105 | Esha | 5 |

### Example 3: Find employees without any department assignment

```sql
SELECT
    EmployeeId,
    EmployeeName,
    DepartmentId
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

Expected result:

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---|
| 107 | Gita | NULL |

Notice the difference:

- Example 1 finds employees without a matching department.
- Example 2 finds employees with an invalid, non-NULL department ID.
- Example 3 finds employees without a department assignment.

---

# PART 3 – NON-MATCHING RECORDS FROM THE RIGHT TABLE

## 5. RIGHT JOIN + WHERE IS NULL

### Concept

A RIGHT JOIN returns:

- All records from the right table.
- Matching records from the left table.
- NULL values for left-table columns when no match exists.

When we filter the left table's primary key using IS NULL, we retrieve records that exist only in the right table.

### Generic syntax

```sql
SELECT
    A.ColumnName,
    B.ColumnName
FROM TableA A
RIGHT JOIN TableB B
    ON A.CommonColumn = B.CommonColumn
WHERE A.PrimaryKey IS NULL;
```

### Example 1: Find departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### Expected result

| DepartmentId | DepartmentName |
|---:|---|
| 4 | Sales |

### Explanation

1. RIGHT JOIN keeps all departments.
2. Matching employee information is retrieved where available.
3. Sales has no matching employee.
4. Employee columns become NULL for Sales.
5. WHERE E.EmployeeId IS NULL returns Sales.

### Example 2: Find departments that have no employees using LEFT JOIN

The same requirement can be written using LEFT JOIN by reversing the table order.

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

Both queries return the same result.

---

# PART 4 – NON-MATCHING RECORDS FROM BOTH TABLES

## 6. FULL OUTER JOIN + WHERE IS NULL

### Concept

FULL OUTER JOIN returns:

- All matching records.
- All unmatched records from the left table.
- All unmatched records from the right table.

To retrieve only non-matching records, use an OR condition with NULL checks on both tables.

### Generic syntax

```sql
SELECT
    A.ColumnName,
    B.ColumnName
FROM TableA A
FULL OUTER JOIN TableB B
    ON A.CommonColumn = B.CommonColumn
WHERE A.PrimaryKey IS NULL
   OR B.PrimaryKey IS NULL;
```

### Example 1: Find unmatched employees and departments

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

### Expected result

| EmployeeId | EmployeeName | EmployeeDepartmentId | MasterDepartmentId | DepartmentName |
|---:|---|---:|---:|---|
| 105 | Esha | 5 | NULL | NULL |
| 107 | Gita | NULL | NULL | NULL |
| NULL | NULL | NULL | 4 | Sales |

### Explanation

**Esha**

- Exists in tblEmployee.
- DepartmentId 5 does not exist in tblDepartment.
- Department columns are NULL.

**Gita**

- Exists in tblEmployee.
- DepartmentId is NULL.
- No department match exists.

**Sales**

- Exists in tblDepartment.
- No employee belongs to DepartmentId 4.
- Employee columns are NULL.

All three records are returned because at least one side of the join is unmatched.

---

## 7. Understanding the Three Main Query Patterns

### Pattern 1: Left-side unmatched records

```sql
SELECT A.*
FROM TableA A
LEFT JOIN TableB B
    ON A.CommonColumn = B.CommonColumn
WHERE B.PrimaryKey IS NULL;
```

Meaning: Records present in TableA but without a matching record in TableB.

### Pattern 2: Right-side unmatched records

```sql
SELECT B.*
FROM TableA A
RIGHT JOIN TableB B
    ON A.CommonColumn = B.CommonColumn
WHERE A.PrimaryKey IS NULL;
```

Meaning: Records present in TableB but without a matching record in TableA.

### Pattern 3: Unmatched records from both sides

```sql
SELECT A.*, B.*
FROM TableA A
FULL OUTER JOIN TableB B
    ON A.CommonColumn = B.CommonColumn
WHERE A.PrimaryKey IS NULL
   OR B.PrimaryKey IS NULL;
```

Meaning: Records that do not have a matching record in the other table.

---

# PART 5 – UNDERSTANDING NULL

## 8. What is NULL?

NULL represents a missing or unknown value.

It is not the same as:

- Zero.
- Empty string.
- Blank space.
- False.

For example:

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

This returns employees whose DepartmentId is NULL.

### Incorrect NULL comparison

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId = NULL;
```

This is not a valid way to test for NULL.

### Correct NULL comparison

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

### Find values that are not NULL

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NOT NULL;
```

## 9. Difference Between IS NULL and IS NOT NULL

| Condition | Meaning |
|---|---|
| IS NULL | Value is missing or unknown |
| IS NOT NULL | Value is present |
| = NULL | Incorrect NULL comparison |
| <> NULL | Incorrect NULL comparison |

### Example 1: Find employees with assigned department IDs

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NOT NULL;
```

### Example 2: Find employees without assigned department IDs

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

### Example 3: Find unmatched department records

```sql
SELECT
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL;
```

Important: In SQL Server, NULL comparisons should use IS NULL and IS NOT NULL. Equality comparisons with NULL evaluate to UNKNOWN rather than TRUE or FALSE.

---

# PART 6 – COMPARISON OF THE THREE TECHNIQUES

## 10. Difference Between LEFT, RIGHT and FULL JOIN Filtering

| Query | What it identifies |
|---|---|
| LEFT JOIN + WHERE RightTable.ID IS NULL | Left-only records |
| RIGHT JOIN + WHERE LeftTable.ID IS NULL | Right-only records |
| FULL OUTER JOIN + WHERE either ID IS NULL | Unmatched records from both sides |

### Sample output using our data

| Technique | Result |
|---|---|
| LEFT JOIN + IS NULL | Esha, Gita |
| RIGHT JOIN + IS NULL | Sales |
| FULL OUTER JOIN + OR IS NULL | Esha, Gita, Sales |

---

# PART 7 – REAL-WORLD SCENARIOS

## 11. Where Are These Techniques Used?

### Scenario 1: Customer and Order Reconciliation

A company has two tables:

- Customer master.
- Order transactions.

Business requirement:

Find customers who have never placed an order.

Solution:

Use LEFT JOIN and check whether the order table's primary key is NULL.

### Scenario 2: Employee and Department Validation

An organization has employee records with department IDs.

Business requirement:

Find employees whose department IDs do not exist in the department master.

Solution:

Use LEFT JOIN and filter unmatched department records. Exclude NULL employee department IDs if only invalid IDs are required.

### Scenario 3: Source and Target Data Validation

During data migration, two systems contain records that should correspond.

Business requirement:

Identify records available in the source but missing in the target, and records available in the target but missing in the source.

Solution:

Use FULL OUTER JOIN and filter unmatched records from both sides.

### Scenario 4: Product and Category Validation

A product table contains category IDs.

Business requirement:

Find products whose category is missing from the category master.

Solution:

Use LEFT JOIN and IS NULL.

### Scenario 5: Student and Course Validation

A student master and course registration table are available.

Business requirement:

Find students who have not registered for any course.

Solution:

Use LEFT JOIN and filter unmatched registration records.

---

# PART 8 – HANDS-ON PRACTICE EXERCISES

Attempt the questions before checking the answer key.

## Level 1 – Beginner

### Scenario 1: Employee and Department

1. Find employees without matching departments.
2. Find departments without employees.
3. Find unmatched records from both tables.
4. Find employees whose DepartmentId is NULL.
5. Find employees whose DepartmentId is invalid.
6. Find all departments that have at least one employee.
7. Find all employees having a valid department.
8. Display only non-matching records from both tables.

### Scenario 2: NULL Practice

1. Find employees with NULL DepartmentId.
2. Find employees with non-NULL DepartmentId.
3. Find unmatched employees using LEFT JOIN.
4. Find unmatched departments using RIGHT JOIN.
5. Find employees with invalid DepartmentId but exclude NULL values.
6. Find unmatched records using FULL OUTER JOIN.
7. Explain why `DepartmentId = NULL` is incorrect.
8. Explain the difference between NULL and an invalid department ID.

---

## Level 2 – Intermediate

### Scenario 3: Advanced Employee Validation

1. Identify employees whose department IDs are missing from the department master.
2. Identify employees who have no department assigned.
3. Identify departments that do not have employees.
4. Identify all records that are not matching between both tables.
5. Write the same query using RIGHT JOIN and LEFT JOIN.
6. Display employee details only for invalid department IDs.
7. Display department details only for departments without employees.
8. Generate a report containing all unmatched records.

### Scenario 4: Filtering Practice

1. Find employees without departments whose EmployeeId is greater than 103.
2. Find unmatched departments whose DepartmentId is greater than 2.
3. Find invalid employee department IDs excluding NULL.
4. Find unmatched records from both tables where either DepartmentId is greater than 2.
5. Find employees who have a department assigned but no matching department record.
6. Find departments that have no employee records.
7. Sort unmatched employee records by EmployeeName.
8. Count the number of unmatched employees.

---

## Level 3 – Real-World Scenarios

### Scenario 5: Customer and Orders

Create the following tables:

```sql
CREATE TABLE tblCustomer
(
    CustomerId INT PRIMARY KEY,
    CustomerName VARCHAR(50)
);

CREATE TABLE tblOrders
(
    OrderId INT PRIMARY KEY,
    CustomerId INT,
    OrderAmount DECIMAL(10,2)
);
```

Insert sample data:

```sql
INSERT INTO tblCustomer
VALUES
(1, 'Ravi'),
(2, 'Priya'),
(3, 'John'),
(4, 'Meena'),
(5, 'Kiran');

INSERT INTO tblOrders
VALUES
(101, 1, 5000),
(102, 2, 3000),
(103, 1, 2500),
(104, 6, 4000);
```

Practice:

1. Find customers who have never placed orders.
2. Find orders that do not have matching customers.
3. Find unmatched records from both tables.
4. Find customers who have placed at least one order.
5. Find customers who have more than one order.
6. Find customers without orders using LEFT JOIN.
7. Find invalid CustomerIds in tblOrders.
8. Generate a reconciliation report.

### Scenario 6: Product and Category

Create the following tables:

```sql
CREATE TABLE tblCategory
(
    CategoryId INT PRIMARY KEY,
    CategoryName VARCHAR(50)
);

CREATE TABLE tblProduct
(
    ProductId INT PRIMARY KEY,
    ProductName VARCHAR(50),
    CategoryId INT
);
```

Insert sample data:

```sql
INSERT INTO tblCategory
VALUES
(1, 'Electronics'),
(2, 'Furniture'),
(3, 'Clothing'),
(4, 'Accessories');

INSERT INTO tblProduct
VALUES
(101, 'Laptop', 1),
(102, 'Chair', 2),
(103, 'Shirt', 3),
(104, 'Mouse', 5),
(105, 'Keyboard', NULL);
```

Practice:

1. Find products without matching categories.
2. Find categories without products.
3. Find unmatched records from both tables.
4. Find products with invalid CategoryIds.
5. Find products without assigned CategoryIds.
6. Find categories that have products.
7. Generate a product-category reconciliation report.
8. Count unmatched products.

### Scenario 7: Source and Target Reconciliation

Create the following tables:

```sql
CREATE TABLE tblSourceEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50)
);

CREATE TABLE tblTargetEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50)
);
```

Insert sample data:

```sql
INSERT INTO tblSourceEmployee
VALUES
(1, 'Arun'),
(2, 'Bala'),
(3, 'Charan'),
(4, 'Deepa'),
(5, 'Esha');

INSERT INTO tblTargetEmployee
VALUES
(1, 'Arun'),
(2, 'Bala'),
(4, 'Deepa'),
(6, 'Farhan');
```

Practice:

1. Find source records missing in the target.
2. Find target records missing in the source.
3. Find records present in both tables.
4. Find all non-matching records.
5. Generate a source-target reconciliation report.
6. Count records missing in the target.
7. Count records missing in the source.
8. Explain how this technique can be used during ETL validation.

---

## Level 4 – Challenge Questions

### Scenario 8: Advanced SQL Challenges

1. Find all employees who have an invalid department ID.
2. Find all departments without employees using LEFT JOIN.
3. Find unmatched employee records using RIGHT JOIN after reversing table order.
4. Find unmatched records from both tables using FULL OUTER JOIN.
5. Write a query to identify NULL department IDs separately from invalid department IDs.
6. Find customers who have never placed an order.
7. Find orders without matching customers.
8. Compare source and target employee records.
9. Generate a report with MatchStatus values such as MATCHED, SOURCE_ONLY and TARGET_ONLY.
10. Explain why FULL OUTER JOIN is useful in migration validation.

---

# PART 9 – ANSWER KEY

Attempt the exercises independently before referring to the solutions.

## Answers – Scenario 1: Employee and Department

### 1. Employees without matching departments

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

### 2. Departments without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 3. Unmatched records from both tables

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

### 4. Employees whose DepartmentId is NULL

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

### 5. Employees with invalid DepartmentId

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

### 6. Departments having at least one employee

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
INNER JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId;
```

To avoid duplicate department rows when several employees belong to one department:

```sql
SELECT DISTINCT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
INNER JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId;
```

### 7. Employees having valid departments

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentName
FROM tblEmployee E
INNER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId;
```

### 8. Only non-matching records

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

---

## Answers – Scenario 2: NULL Practice

### 1. Employees with NULL DepartmentId

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

### 2. Employees with non-NULL DepartmentId

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NOT NULL;
```

### 3. Unmatched employees using LEFT JOIN

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL;
```

### 4. Unmatched departments using RIGHT JOIN

```sql
SELECT D.*
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 5. Invalid DepartmentId excluding NULL

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 6. Unmatched records using FULL OUTER JOIN

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL
   OR D.DepartmentId IS NULL;
```

### 7. Why is DepartmentId = NULL incorrect?

NULL represents an unknown or missing value. SQL Server does not evaluate equality with NULL as TRUE. Use IS NULL instead.

### 8. NULL versus invalid department ID

- NULL: No department value has been assigned.
- Invalid ID: A department value exists, but no matching record exists in the department master.

---

## Answers – Scenario 3: Advanced Employee Validation

### 1. Invalid department IDs

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

### 2. Employees without department assignments

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

### 3. Departments without employees

```sql
SELECT D.*
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 4. All non-matching records

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

### 5. Same result using RIGHT JOIN and LEFT JOIN

RIGHT JOIN:

```sql
SELECT E.*
FROM tblEmployee E
RIGHT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL;
```

Equivalent LEFT JOIN:

```sql
SELECT D.*
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

Both identify departments without employees.

### 6. Employee details with invalid department IDs

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

### 7. Department details without employees

```sql
SELECT
    D.DepartmentId,
    D.DepartmentName
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 8. Report containing all unmatched records

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

---

## Answers – Scenario 4: Filtering Practice

### 1. Employees without departments with EmployeeId greater than 103

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL
  AND E.EmployeeId > 103;
```

### 2. Unmatched departments with DepartmentId greater than 2

```sql
SELECT D.*
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL
  AND D.DepartmentId > 2;
```

### 3. Invalid employee department IDs excluding NULL

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 4. Unmatched records where either DepartmentId is greater than 2

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
WHERE
    (
        E.EmployeeId IS NULL
        OR D.DepartmentId IS NULL
    )
    AND
    (
        E.DepartmentId > 2
        OR D.DepartmentId > 2
    );
```

### 5. Employees with assigned but unmatched department IDs

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 6. Departments without employee records

```sql
SELECT D.*
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 7. Sort unmatched employees by name

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.DepartmentId
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL
ORDER BY E.EmployeeName;
```

### 8. Count unmatched employees

```sql
SELECT
    COUNT(*) AS TotalUnmatchedEmployees
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE D.DepartmentId IS NULL;
```

Expected result: 2.

---

## Answers – Scenario 5: Customer and Orders

### 1. Customers who never placed orders

```sql
SELECT
    C.CustomerId,
    C.CustomerName
FROM tblCustomer C
LEFT JOIN tblOrders O
    ON C.CustomerId = O.CustomerId
WHERE O.OrderId IS NULL;
```

Expected result:

| CustomerId | CustomerName |
|---:|---|
| 3 | John |
| 4 | Meena |
| 5 | Kiran |

### 2. Orders without matching customers

```sql
SELECT
    O.OrderId,
    O.CustomerId,
    O.OrderAmount
FROM tblOrders O
LEFT JOIN tblCustomer C
    ON O.CustomerId = C.CustomerId
WHERE C.CustomerId IS NULL;
```

Expected result:

| OrderId | CustomerId | OrderAmount |
|---:|---:|---:|
| 104 | 6 | 4000 |

### 3. Unmatched records from both tables

```sql
SELECT
    C.CustomerId,
    C.CustomerName,
    O.OrderId,
    O.CustomerId AS OrderCustomerId,
    O.OrderAmount
FROM tblCustomer C
FULL OUTER JOIN tblOrders O
    ON C.CustomerId = O.CustomerId
WHERE C.CustomerId IS NULL
   OR O.OrderId IS NULL;
```

### 4. Customers who have placed orders

```sql
SELECT DISTINCT
    C.CustomerId,
    C.CustomerName
FROM tblCustomer C
INNER JOIN tblOrders O
    ON C.CustomerId = O.CustomerId;
```

### 5. Customers with more than one order

```sql
SELECT
    C.CustomerId,
    C.CustomerName,
    COUNT(O.OrderId) AS TotalOrders
FROM tblCustomer C
INNER JOIN tblOrders O
    ON C.CustomerId = O.CustomerId
GROUP BY
    C.CustomerId,
    C.CustomerName
HAVING COUNT(O.OrderId) > 1;
```

Expected result: Ravi with 2 orders.

### 6. Customers without orders using LEFT JOIN

```sql
SELECT C.*
FROM tblCustomer C
LEFT JOIN tblOrders O
    ON C.CustomerId = O.CustomerId
WHERE O.OrderId IS NULL;
```

### 7. Invalid CustomerIds in orders

```sql
SELECT O.*
FROM tblOrders O
LEFT JOIN tblCustomer C
    ON O.CustomerId = C.CustomerId
WHERE O.CustomerId IS NOT NULL
  AND C.CustomerId IS NULL;
```

### 8. Customer and order reconciliation report

```sql
SELECT
    C.CustomerId,
    C.CustomerName,
    O.OrderId,
    O.CustomerId AS OrderCustomerId,
    CASE
        WHEN C.CustomerId IS NULL
            THEN 'ORDER_WITHOUT_CUSTOMER'
        WHEN O.OrderId IS NULL
            THEN 'CUSTOMER_WITHOUT_ORDER'
        ELSE 'MATCHED'
    END AS ReconciliationStatus
FROM tblCustomer C
FULL OUTER JOIN tblOrders O
    ON C.CustomerId = O.CustomerId;
```

---

## Answers – Scenario 6: Product and Category

### 1. Products without matching categories

```sql
SELECT P.*
FROM tblProduct P
LEFT JOIN tblCategory C
    ON P.CategoryId = C.CategoryId
WHERE C.CategoryId IS NULL;
```

### 2. Categories without products

```sql
SELECT C.*
FROM tblCategory C
LEFT JOIN tblProduct P
    ON C.CategoryId = P.CategoryId
WHERE P.ProductId IS NULL;
```

### 3. Unmatched records from both tables

```sql
SELECT
    P.ProductId,
    P.ProductName,
    P.CategoryId AS ProductCategoryId,
    C.CategoryId AS MasterCategoryId,
    C.CategoryName
FROM tblProduct P
FULL OUTER JOIN tblCategory C
    ON P.CategoryId = C.CategoryId
WHERE P.ProductId IS NULL
   OR C.CategoryId IS NULL;
```

### 4. Products with invalid CategoryIds

```sql
SELECT P.*
FROM tblProduct P
LEFT JOIN tblCategory C
    ON P.CategoryId = C.CategoryId
WHERE P.CategoryId IS NOT NULL
  AND C.CategoryId IS NULL;
```

### 5. Products without assigned CategoryIds

```sql
SELECT *
FROM tblProduct
WHERE CategoryId IS NULL;
```

### 6. Categories that have products

```sql
SELECT DISTINCT
    C.CategoryId,
    C.CategoryName
FROM tblCategory C
INNER JOIN tblProduct P
    ON C.CategoryId = P.CategoryId;
```

### 7. Product-category reconciliation report

```sql
SELECT
    P.ProductId,
    P.ProductName,
    P.CategoryId AS ProductCategoryId,
    C.CategoryId AS MasterCategoryId,
    C.CategoryName,
    CASE
        WHEN P.ProductId IS NULL
            THEN 'CATEGORY_WITHOUT_PRODUCT'
        WHEN P.CategoryId IS NULL
            THEN 'PRODUCT_WITHOUT_CATEGORY'
        WHEN C.CategoryId IS NULL
            THEN 'INVALID_CATEGORY_ID'
        ELSE 'MATCHED'
    END AS ReconciliationStatus
FROM tblProduct P
FULL OUTER JOIN tblCategory C
    ON P.CategoryId = C.CategoryId;
```

### 8. Count unmatched products

```sql
SELECT COUNT(*) AS TotalUnmatchedProducts
FROM tblProduct P
LEFT JOIN tblCategory C
    ON P.CategoryId = C.CategoryId
WHERE C.CategoryId IS NULL;
```

Expected result: 2.

---

## Answers – Scenario 7: Source and Target Reconciliation

### 1. Source records missing in target

```sql
SELECT
    S.EmployeeId,
    S.EmployeeName
FROM tblSourceEmployee S
LEFT JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId
WHERE T.EmployeeId IS NULL;
```

Expected result:

| EmployeeId | EmployeeName |
|---:|---|
| 3 | Charan |
| 5 | Esha |

### 2. Target records missing in source

```sql
SELECT
    T.EmployeeId,
    T.EmployeeName
FROM tblTargetEmployee T
LEFT JOIN tblSourceEmployee S
    ON T.EmployeeId = S.EmployeeId
WHERE S.EmployeeId IS NULL;
```

Expected result:

| EmployeeId | EmployeeName |
|---:|---|
| 6 | Farhan |

### 3. Records present in both tables

```sql
SELECT
    S.EmployeeId,
    S.EmployeeName
FROM tblSourceEmployee S
INNER JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId;
```

### 4. All non-matching records

```sql
SELECT
    S.EmployeeId AS SourceEmployeeId,
    S.EmployeeName AS SourceEmployeeName,
    T.EmployeeId AS TargetEmployeeId,
    T.EmployeeName AS TargetEmployeeName
FROM tblSourceEmployee S
FULL OUTER JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId
WHERE S.EmployeeId IS NULL
   OR T.EmployeeId IS NULL;
```

### 5. Source-target reconciliation report

```sql
SELECT
    COALESCE(S.EmployeeId, T.EmployeeId) AS EmployeeId,
    S.EmployeeName AS SourceEmployeeName,
    T.EmployeeName AS TargetEmployeeName,
    CASE
        WHEN S.EmployeeId IS NULL
            THEN 'TARGET_ONLY'
        WHEN T.EmployeeId IS NULL
            THEN 'SOURCE_ONLY'
        ELSE 'MATCHED'
    END AS MatchStatus
FROM tblSourceEmployee S
FULL OUTER JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId;
```

### 6. Count records missing in target

```sql
SELECT COUNT(*) AS MissingInTarget
FROM tblSourceEmployee S
LEFT JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId
WHERE T.EmployeeId IS NULL;
```

Expected result: 2.

### 7. Count records missing in source

```sql
SELECT COUNT(*) AS MissingInSource
FROM tblTargetEmployee T
LEFT JOIN tblSourceEmployee S
    ON T.EmployeeId = S.EmployeeId
WHERE S.EmployeeId IS NULL;
```

Expected result: 1.

### 8. How is this used in ETL validation?

During migration:

1. Extract source records.
2. Load records into the target.
3. Compare source and target using a common key.
4. Identify records missing in the target.
5. Identify unexpected records in the target.
6. Investigate and resolve discrepancies.

---

## Answers – Scenario 8: Advanced SQL Challenges

### 1. Employees with invalid department IDs

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 2. Departments without employees using LEFT JOIN

```sql
SELECT D.*
FROM tblDepartment D
LEFT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE E.EmployeeId IS NULL;
```

### 3. Unmatched employees using RIGHT JOIN after reversing table order

```sql
SELECT E.*
FROM tblDepartment D
RIGHT JOIN tblEmployee E
    ON D.DepartmentId = E.DepartmentId
WHERE D.DepartmentId IS NULL;
```

### 4. Unmatched records from both tables

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    D.DepartmentId,
    D.DepartmentName
FROM tblEmployee E
FULL OUTER JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.EmployeeId IS NULL
   OR D.DepartmentId IS NULL;
```

### 5. Identify NULL department IDs separately from invalid IDs

NULL department IDs:

```sql
SELECT *
FROM tblEmployee
WHERE DepartmentId IS NULL;
```

Invalid department IDs:

```sql
SELECT E.*
FROM tblEmployee E
LEFT JOIN tblDepartment D
    ON E.DepartmentId = D.DepartmentId
WHERE E.DepartmentId IS NOT NULL
  AND D.DepartmentId IS NULL;
```

### 6. Customers who never placed orders

```sql
SELECT C.*
FROM tblCustomer C
LEFT JOIN tblOrders O
    ON C.CustomerId = O.CustomerId
WHERE O.OrderId IS NULL;
```

### 7. Orders without matching customers

```sql
SELECT O.*
FROM tblOrders O
LEFT JOIN tblCustomer C
    ON O.CustomerId = C.CustomerId
WHERE C.CustomerId IS NULL;
```

### 8. Compare source and target employee records

```sql
SELECT
    S.EmployeeId AS SourceEmployeeId,
    T.EmployeeId AS TargetEmployeeId,
    S.EmployeeName AS SourceEmployeeName,
    T.EmployeeName AS TargetEmployeeName
FROM tblSourceEmployee S
FULL OUTER JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId;
```

### 9. MatchStatus report

```sql
SELECT
    COALESCE(S.EmployeeId, T.EmployeeId) AS EmployeeId,
    S.EmployeeName AS SourceEmployeeName,
    T.EmployeeName AS TargetEmployeeName,
    CASE
        WHEN S.EmployeeId IS NULL
            THEN 'TARGET_ONLY'
        WHEN T.EmployeeId IS NULL
            THEN 'SOURCE_ONLY'
        ELSE 'MATCHED'
    END AS MatchStatus
FROM tblSourceEmployee S
FULL OUTER JOIN tblTargetEmployee T
    ON S.EmployeeId = T.EmployeeId;
```

### 10. Why is FULL OUTER JOIN useful in migration validation?

It helps identify records that exist in the source but not in the target, and records that exist in the target but not in the source. It provides a combined view of both unmatched sets.

---

# PART 10 – REVISION AND INTERVIEW QUESTIONS

1. What is a non-matching record?
2. How can we find records present only in the left table?
3. How can we find records present only in the right table?
4. How can we find unmatched records from both tables?
5. Why do we use IS NULL after a LEFT JOIN?
6. Why do we use IS NULL after a RIGHT JOIN?
7. What is the purpose of FULL OUTER JOIN?
8. Why is `= NULL` incorrect?
9. What is the difference between NULL and an invalid foreign key?
10. How can we identify invalid department IDs?
11. What happens when we use IS NOT NULL?
12. Can LEFT JOIN be rewritten as RIGHT JOIN by changing table order?
13. How can we identify customers who never placed orders?
14. How can FULL OUTER JOIN help in source-target reconciliation?
15. What is the difference between SOURCE_ONLY and TARGET_ONLY?
16. Why should we use a non-nullable primary key for unmatched-row detection?
17. How would you identify missing records after an ETL load?
18. What happens when multiple records have the same join key?

---

# PART 11 – FINAL PRACTICAL CHECKLIST

- [ ] Understand matching and non-matching records.
- [ ] Create and populate tblEmployee and tblDepartment.
- [ ] Retrieve unmatched left-table records.
- [ ] Retrieve unmatched right-table records.
- [ ] Retrieve unmatched records from both tables.
- [ ] Use IS NULL correctly.
- [ ] Use IS NOT NULL correctly.
- [ ] Identify NULL department IDs.
- [ ] Identify invalid department IDs.
- [ ] Build a customer-order validation query.
- [ ] Build a product-category validation query.
- [ ] Build a source-target reconciliation report.
- [ ] Complete the challenge exercises independently.

---

# PART 12 – OFFICIAL MICROSOFT DOCUMENTATION

1. FROM and JOIN:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/from-transact-sql

2. SELECT statement:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql

3. NULL and UNKNOWN:
   https://learn.microsoft.com/en-us/sql/t-sql/language-elements/null-and-unknown-transact-sql

4. IS NULL:
   https://learn.microsoft.com/en-us/sql/t-sql/queries/is-null-transact-sql

---

## IMPORTANT LEARNING NOTES

Remember these three query patterns:

### Left-only records

```sql
SELECT A.*
FROM TableA A
LEFT JOIN TableB B
    ON A.ID = B.ID
WHERE B.ID IS NULL;
```

### Right-only records

```sql
SELECT B.*
FROM TableA A
RIGHT JOIN TableB B
    ON A.ID = B.ID
WHERE A.ID IS NULL;
```

### Unmatched records from both tables

```sql
SELECT A.*, B.*
FROM TableA A
FULL OUTER JOIN TableB B
    ON A.ID = B.ID
WHERE A.ID IS NULL
   OR B.ID IS NULL;
```

**The most important takeaway:**

- LEFT JOIN + IS NULL = Left-only records.
- RIGHT JOIN + IS NULL = Right-only records.
- FULL OUTER JOIN + OR + IS NULL = Unmatched records from both sides.
- IS NULL is the correct way to check for NULL values.

The goal is to understand how to identify missing, invalid and unmatched records in relational databases.
