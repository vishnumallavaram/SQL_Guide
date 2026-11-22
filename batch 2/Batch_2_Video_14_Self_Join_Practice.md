
# Batch 2 – Video 14: Self JOIN in SQL Server

## 1. Learning Objectives

By the end of this practice, you will understand:

- What a Self JOIN is.
- Why we use aliases when joining a table to itself.
- How to retrieve employee and manager names from one table.
- How INNER SELF JOIN works.
- How LEFT, RIGHT, and FULL OUTER SELF JOINs work.
- How CROSS SELF JOIN produces combinations of rows.
- How Self JOINs are used in real-world scenarios.

---

## 2. What Is a Self JOIN?

A Self JOIN is a technique where a table is joined with itself.

It is useful when records in the same table are related to one another.

For example, consider an employee table where each employee has a `ManagerId`.

The `ManagerId` refers to another employee's `EmployeeId` in the same table.

We can use a Self JOIN to retrieve:

- Employee name
- Manager name

**Important:** Self JOIN is not a separate JOIN type. It is a technique that uses the same table more than once with different aliases.

---

## 3. Create the Sample Table

We will use one table named `tblEmployee`.

### Table structure

| Column | Description |
|---|---|
| EmployeeId | Unique employee ID |
| EmployeeName | Employee name |
| ManagerId | Employee ID of the manager |

### Create table

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    ManagerId INT NULL,

    CONSTRAINT FK_tblEmployee_Manager
        FOREIGN KEY (ManagerId)
        REFERENCES tblEmployee(EmployeeId)
);
```

### Insert sample records

```sql
INSERT INTO tblEmployee
    (EmployeeId, EmployeeName, ManagerId)
VALUES
    (1, 'Arun', NULL),
    (2, 'Bala', 1),
    (3, 'Charan', 1),
    (4, 'Deepa', 2),
    (5, 'Esha', 2),
    (6, 'Farhan', 3);
```

### View the records

```sql
SELECT *
FROM tblEmployee;
```

### Expected output

| EmployeeId | EmployeeName | ManagerId |
|---:|---|---:|
| 1 | Arun | NULL |
| 2 | Bala | 1 |
| 3 | Charan | 1 |
| 4 | Deepa | 2 |
| 5 | Esha | 2 |
| 6 | Farhan | 3 |

### Understand the relationship

| Employee | Manager |
|---|---|
| Arun | No manager assigned |
| Bala | Arun |
| Charan | Arun |
| Deepa | Bala |
| Esha | Bala |
| Farhan | Charan |

---

## 4. Understanding Table Aliases

When we join a table with itself, we need to refer to the two instances separately.

We use aliases:

- `E` represents the employee.
- `M` represents the manager.

Although both aliases refer to `tblEmployee`, SQL Server treats them as separate references in the query.

### Basic Self JOIN structure

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

### Understand the JOIN condition

```sql
ON E.ManagerId = M.EmployeeId
```

This means:

1. Take the employee's ManagerId.
2. Find the employee record whose EmployeeId matches it.
3. Return that person's name as the manager name.

---

## 5. INNER SELF JOIN

An INNER SELF JOIN returns only matching records.

### Query

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

### Expected output

| EmployeeId | EmployeeName | ManagerName |
|---:|---|---|
| 2 | Bala | Arun |
| 3 | Charan | Arun |
| 4 | Deepa | Bala |
| 5 | Esha | Bala |
| 6 | Farhan | Charan |

### Important observations

- Bala reports to Arun.
- Charan reports to Arun.
- Deepa and Esha report to Bala.
- Farhan reports to Charan.
- Arun does not appear because there is no matching manager record for Arun's NULL ManagerId.

**Remember:** INNER JOIN returns only matching rows.

---

## 6. LEFT OUTER SELF JOIN

A LEFT SELF JOIN returns:

- All records from the left table reference (`E`).
- Matching records from the right table reference (`M`).
- NULL values for manager columns when no matching manager exists.

### Query

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

### Expected output

| EmployeeId | EmployeeName | ManagerName |
|---:|---|---|
| 1 | Arun | NULL |
| 2 | Bala | Arun |
| 3 | Charan | Arun |
| 4 | Deepa | Bala |
| 5 | Esha | Bala |
| 6 | Farhan | Charan |

### Important observations

- All six employees are returned.
- Employees with matching managers show the manager's name.
- Arun is included even though no manager is assigned.

**Remember:** LEFT JOIN preserves every row from the left-side reference.

---

## 7. RIGHT OUTER SELF JOIN

A RIGHT SELF JOIN returns:

- All records from the right table reference (`M`).
- Matching records from the left table reference (`E`).
- NULL values for employee columns when no matching employee is found.

### Query

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
RIGHT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
ORDER BY M.EmployeeId, E.EmployeeId;
```

### Expected output

| EmployeeName | ManagerName |
|---|---|
| Bala | Arun |
| Charan | Arun |
| Deepa | Bala |
| Esha | Bala |
| Farhan | Charan |
| NULL | Deepa |
| NULL | Esha |
| NULL | Farhan |

### Important observations

- Every record from the manager-side reference (`M`) is preserved.
- Arun has two matching employees.
- Bala has two matching employees.
- Charan has one matching employee.
- Deepa, Esha, and Farhan have no direct reports, so their employee-side values are NULL.

**Remember:** RIGHT JOIN preserves every row from the right-side reference.

---

## 8. FULL OUTER SELF JOIN

A FULL OUTER SELF JOIN returns:

- Matching records.
- Non-matching records from the left-side reference.
- Non-matching records from the right-side reference.

### Query

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
FULL OUTER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
ORDER BY
    M.EmployeeId,
    E.EmployeeId;
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
| NULL | Deepa |
| NULL | Esha |
| NULL | Farhan |

### Important observations

- All employee-side records are preserved.
- All manager-side records are preserved.
- Arun appears as an employee without a manager.
- Deepa, Esha, and Farhan appear on the manager side without direct reports.

**Remember:** FULL OUTER JOIN preserves unmatched rows from both sides.

---

## 9. CROSS SELF JOIN

A CROSS SELF JOIN produces every possible combination between the two references of the same table.

There is no ON condition.

### Query

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
CROSS JOIN tblEmployee M;
```

### Expected number of rows

We have six records in the table.

Formula:

`Number of rows = Number of rows in E × Number of rows in M`

`6 × 6 = 36`

Therefore, the query returns 36 combinations.

### Sample output

| EmployeeName | ManagerName |
|---|---|
| Arun | Arun |
| Arun | Bala |
| Arun | Charan |
| Arun | Deepa |
| Arun | Esha |
| Arun | Farhan |
| Bala | Arun |
| Bala | Bala |
| ... | ... |

**Remember:** CROSS JOIN creates a Cartesian product.

---

## 10. Comparison of Self JOIN Types

| JOIN Type | What it returns |
|---|---|
| INNER SELF JOIN | Only matching records |
| LEFT SELF JOIN | All left-side records and matching right-side records |
| RIGHT SELF JOIN | All right-side records and matching left-side records |
| FULL OUTER SELF JOIN | All records from both references |
| CROSS SELF JOIN | Every possible combination |

### Expected row counts for our sample

| JOIN Type | Expected Rows |
|---|---:|
| INNER SELF JOIN | 5 |
| LEFT SELF JOIN | 6 |
| RIGHT SELF JOIN | 8 |
| FULL OUTER SELF JOIN | 9 |
| CROSS SELF JOIN | 36 |

---

# 11. Hands-on Practice Exercises

Try writing the queries yourself before checking the solutions.

## Beginner Level

### Exercise 1
Display employee names along with their manager names using INNER SELF JOIN.

### Exercise 2
Display all employees along with their manager names using LEFT SELF JOIN.

### Exercise 3
Display only the employee name and manager name. Use meaningful column aliases.

### Exercise 4
Display the employee ID, employee name, and manager ID using a Self JOIN.

### Exercise 5
Find the number of employee-manager matching records using INNER SELF JOIN.

---

## Intermediate Level

### Exercise 6
Display employees who report to Arun.

### Exercise 7
Display employees who report to Bala.

### Exercise 8
Display employees who report to the same manager.

### Exercise 9
Display the employee name, manager name, and manager ID. Sort the output by ManagerId.

### Exercise 10
Display the employees whose manager is Charan.

### Exercise 11
Display the employee-manager combinations using RIGHT SELF JOIN.

### Exercise 12
Display all possible employee and manager combinations using CROSS SELF JOIN.

---

## Real-World Practice

### Exercise 13
You are given an employee table containing employee and manager relationships.

Write a query to display:

- Employee ID
- Employee name
- Manager name

Use LEFT SELF JOIN.

### Exercise 14
Display the number of direct reports for each manager.

### Exercise 15
Display managers who have more than one direct report.

### Exercise 16
Display employee names and their manager names, but only for employees whose manager ID is 1.

### Exercise 17
Display all employees and the employee ID of their manager using INNER SELF JOIN.

### Exercise 18
Use FULL OUTER SELF JOIN to understand which rows do not have a matching row on the other side.

---

# 12. Complete Answer Key

## Answer 1 – Employee and manager names

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 2 – All employees and their managers

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 3 – Meaningful column aliases

```sql
SELECT
    E.EmployeeName AS Employee,
    M.EmployeeName AS Manager
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 4 – Employee ID, name, and manager ID

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    E.ManagerId
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 5 – Count matching employee-manager records

```sql
SELECT COUNT(*) AS EmployeeManagerCount
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

Expected result: `5`

## Answer 6 – Employees reporting to Arun

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
WHERE M.EmployeeName = 'Arun';
```

Expected employees: Bala, Charan.

## Answer 7 – Employees reporting to Bala

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
WHERE M.EmployeeName = 'Bala';
```

Expected employees: Deepa, Esha.

## Answer 8 – Employees reporting to the same manager

```sql
SELECT
    E1.EmployeeName AS Employee1,
    E2.EmployeeName AS Employee2,
    M.EmployeeName AS ManagerName
FROM tblEmployee E1
INNER JOIN tblEmployee E2
    ON E1.ManagerId = E2.ManagerId
INNER JOIN tblEmployee M
    ON E1.ManagerId = M.EmployeeId
WHERE E1.EmployeeId < E2.EmployeeId;
```

Expected combinations:

| Employee1 | Employee2 | ManagerName |
|---|---|---|
| Bala | Charan | Arun |
| Deepa | Esha | Bala |

The condition `E1.EmployeeId < E2.EmployeeId` avoids duplicate pairs and prevents an employee from being paired with themselves.

## Answer 9 – Sort by ManagerId

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName,
    E.ManagerId
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
ORDER BY E.ManagerId;
```

## Answer 10 – Employees whose manager is Charan

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
WHERE M.EmployeeName = 'Charan';
```

Expected employee: Farhan.

## Answer 11 – RIGHT SELF JOIN

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
RIGHT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 12 – CROSS SELF JOIN

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
CROSS JOIN tblEmployee M;
```

Expected result: 36 rows.

## Answer 13 – Employee and manager details

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
LEFT JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 14 – Number of direct reports for each manager

```sql
SELECT
    M.EmployeeId AS ManagerId,
    M.EmployeeName AS ManagerName,
    COUNT(E.EmployeeId) AS DirectReportCount
FROM tblEmployee M
LEFT JOIN tblEmployee E
    ON E.ManagerId = M.EmployeeId
GROUP BY
    M.EmployeeId,
    M.EmployeeName
ORDER BY M.EmployeeId;
```

Expected output:

| ManagerId | ManagerName | DirectReportCount |
|---:|---|---:|
| 1 | Arun | 2 |
| 2 | Bala | 2 |
| 3 | Charan | 1 |
| 4 | Deepa | 0 |
| 5 | Esha | 0 |
| 6 | Farhan | 0 |

## Answer 15 – Managers with more than one direct report

```sql
SELECT
    M.EmployeeName AS ManagerName,
    COUNT(E.EmployeeId) AS DirectReportCount
FROM tblEmployee M
INNER JOIN tblEmployee E
    ON E.ManagerId = M.EmployeeId
GROUP BY
    M.EmployeeId,
    M.EmployeeName
HAVING COUNT(E.EmployeeId) > 1;
```

Expected managers: Arun and Bala.

## Answer 16 – Employees whose manager ID is 1

```sql
SELECT
    E.EmployeeName AS EmployeeName,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId
WHERE E.ManagerId = 1;
```

Expected employees: Bala, Charan.

## Answer 17 – Employee and manager IDs

```sql
SELECT
    E.EmployeeId,
    E.EmployeeName,
    M.EmployeeId AS ManagerEmployeeId
FROM tblEmployee E
INNER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

## Answer 18 – FULL OUTER SELF JOIN

```sql
SELECT
    E.EmployeeId AS EmployeeId,
    E.EmployeeName AS EmployeeName,
    M.EmployeeId AS ManagerEmployeeId,
    M.EmployeeName AS ManagerName
FROM tblEmployee E
FULL OUTER JOIN tblEmployee M
    ON E.ManagerId = M.EmployeeId;
```

This returns nine rows for the sample data. It includes the employee-side row for Arun, who has no manager, and the manager-side rows for employees who have no direct reports.

---

# 13. Important SQL Concepts to Remember

1. Self JOIN joins a table with itself.
2. Self JOIN is not a separate JOIN type.
3. Aliases help distinguish the two references of the same table.
4. INNER JOIN returns matching records.
5. LEFT JOIN preserves all rows from the left reference.
6. RIGHT JOIN preserves all rows from the right reference.
7. FULL OUTER JOIN preserves unmatched rows from both references.
8. CROSS JOIN returns every possible combination.
9. The JOIN condition determines how the two references are matched.
10. A table can have a relationship with itself through a foreign key.

---

# 14. Revision Questions

1. What is a Self JOIN?
2. Is Self JOIN a separate JOIN type?
3. Why do we use aliases in Self JOIN?
4. What is the difference between INNER SELF JOIN and LEFT SELF JOIN?
5. Can we use RIGHT JOIN in a Self JOIN?
6. Can we use FULL OUTER JOIN in a Self JOIN?
7. Does CROSS JOIN require an ON condition?
8. What is the purpose of ManagerId in an employee table?
9. How do we find employees reporting to a particular manager?
10. How do we count the direct reports of each manager?

---

# 15. Completion Checklist

- [ ] I understand what Self JOIN means.
- [ ] I understand why aliases are required.
- [ ] I can write an INNER SELF JOIN.
- [ ] I can write a LEFT SELF JOIN.
- [ ] I understand RIGHT and FULL OUTER SELF JOIN.
- [ ] I understand CROSS SELF JOIN.
- [ ] I can retrieve employee and manager names.
- [ ] I can find employees reporting to a specific manager.
- [ ] I can count direct reports for each manager.
- [ ] I completed all 18 exercises.

---

# 16. Microsoft Documentation

- [FROM (Transact-SQL) – JOIN syntax](https://learn.microsoft.com/en-us/sql/t-sql/queries/from-transact-sql)
- [JOIN Fundamentals](https://learn.microsoft.com/en-us/sql/relational-databases/performance/joins)
- [Primary and Foreign Key Constraints](https://learn.microsoft.com/en-us/sql/relational-databases/tables/primary-and-foreign-key-constraints)

---

**Practice recommendation:** First write all five JOIN queries without looking at the answers. Then complete the exercises and verify the expected row counts.
