
# Batch 2 – Video 18: Stored Procedures in SQL Server

## 1. Learning Objectives

By the end of this lesson, you will understand:

- What a stored procedure is.
- Why stored procedures are used.
- How to create a stored procedure.
- How to execute a stored procedure.
- How to pass input parameters.
- Difference between positional and named parameters.
- How to view a stored procedure definition.
- How to modify and delete stored procedures.
- Basic introduction to WITH ENCRYPTION.

---

# 2. What Is a Stored Procedure?

A stored procedure is a group of SQL statements saved in a database so that we can execute and reuse them whenever required.

Instead of writing the same SQL query repeatedly, we can create a stored procedure once and execute it whenever needed.

### Example

Suppose we frequently execute this query:

```sql
SELECT *
FROM tblEmployee;
```

Instead of writing it repeatedly, we can save it as a stored procedure.

```sql
CREATE PROCEDURE usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

Now we can execute the stored procedure whenever required.

```sql
EXEC usp_GetAllEmployees;
```

### Advantages of Stored Procedures

1. Reusability – Write once and execute multiple times.
2. Maintainability – Modify the procedure in one place.
3. Organization – Keep related SQL statements together.
4. Parameters – Pass values dynamically.
5. Reduced repeated SQL writing – Applications can call saved procedures.

---

# 3. Create Sample Employee Table

We will use a simple employee table for all examples.

### Create table

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    Department VARCHAR(50),
    Salary DECIMAL(10,2)
);
```

### Insert sample records

```sql
INSERT INTO tblEmployee
VALUES
    (101, 'Arun', 'IT', 50000),
    (102, 'Bala', 'HR', 45000),
    (103, 'Charan', 'Finance', 60000),
    (104, 'Deepa', 'IT', 55000),
    (105, 'Esha', 'Sales', 40000);
```

### View the records

```sql
SELECT *
FROM tblEmployee;
```

### Expected output

| EmployeeId | EmployeeName | Department | Salary |
|---:|---|---|---:|
| 101 | Arun | IT | 50000 |
| 102 | Bala | HR | 45000 |
| 103 | Charan | Finance | 60000 |
| 104 | Deepa | IT | 55000 |
| 105 | Esha | Sales | 40000 |

---

# 4. Creating a Stored Procedure

We use `CREATE PROCEDURE` or its shorter form `CREATE PROC` to create a stored procedure.

### Syntax

```sql
CREATE PROCEDURE ProcedureName
AS
BEGIN
    -- SQL statements
END;
```

### Example 1 – Create a procedure to retrieve all employees

```sql
CREATE PROCEDURE usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

Here:

- `CREATE PROCEDURE` creates the procedure.
- `usp_GetAllEmployees` is the procedure name.
- `AS` separates the procedure name from its body.
- `BEGIN` and `END` group the SQL statements.

### Alternative using CREATE PROC

```sql
CREATE PROC usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

Both commands create a stored procedure.

**Naming best practice:** Avoid using `sp_` as a prefix for user-defined stored procedures. SQL Server uses this prefix for many system stored procedures.

---

# 5. Executing a Stored Procedure

After creating a stored procedure, we can execute it using `EXEC` or `EXECUTE`.

### Method 1 – Using EXEC

```sql
EXEC usp_GetAllEmployees;
```

### Method 2 – Using EXECUTE

```sql
EXECUTE usp_GetAllEmployees;
```

Both statements execute the stored procedure.

### Expected output

| EmployeeId | EmployeeName | Department | Salary |
|---:|---|---|---:|
| 101 | Arun | IT | 50000 |
| 102 | Bala | HR | 45000 |
| 103 | Charan | Finance | 60000 |
| 104 | Deepa | IT | 55000 |
| 105 | Esha | Sales | 40000 |

### Execute through SSMS

1. Open SQL Server Management Studio.
2. Connect to your database.
3. Expand Databases.
4. Expand your database.
5. Expand Programmability.
6. Expand Stored Procedures.
7. Find the procedure.
8. Right-click and select Execute Stored Procedure.

---

# 6. Stored Procedures with Input Parameters

Parameters allow us to pass values dynamically.

Instead of creating separate procedures for every employee, we can create one procedure and pass an EmployeeId.

### Example – Get employee by ID

```sql
CREATE PROCEDURE usp_GetEmployeeById
    @EmployeeId INT
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE EmployeeId = @EmployeeId;
END;
```

### Execute the procedure

```sql
EXEC usp_GetEmployeeById 101;
```

### Expected output

| EmployeeId | EmployeeName | Department | Salary |
|---:|---|---|---:|
| 101 | Arun | IT | 50000 |

We can also pass another employee ID.

```sql
EXEC usp_GetEmployeeById 103;
```

Expected output: Charan's record.

### Understanding the parameter

```sql
@EmployeeId INT
```

- `@EmployeeId` is the parameter name.
- `INT` is its data type.
- The value is supplied when executing the procedure.

---

# 7. Passing Multiple Parameters

We can pass multiple parameters to a stored procedure.

### Example – Get employees by department and minimum salary

```sql
CREATE PROCEDURE usp_GetEmployeesByDepartment
    @Department VARCHAR(50),
    @MinimumSalary DECIMAL(10,2)
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Department = @Department
      AND Salary >= @MinimumSalary;
END;
```

### Execute the procedure

```sql
EXEC usp_GetEmployeesByDepartment 'IT', 50000;
```

### Expected output

| EmployeeId | EmployeeName | Department | Salary |
|---:|---|---|---:|
| 101 | Arun | IT | 50000 |
| 104 | Deepa | IT | 55000 |

---

# 8. Positional and Named Parameters

SQL Server allows us to pass parameter values in two ways.

## Method 1 – Positional parameters

Values are passed in the same order in which parameters are declared.

Procedure:

```sql
CREATE PROCEDURE usp_GetEmployeeDetails
    @Department VARCHAR(50),
    @MinimumSalary DECIMAL(10,2)
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Department = @Department
      AND Salary >= @MinimumSalary;
END;
```

Execution:

```sql
EXEC usp_GetEmployeeDetails 'IT', 50000;
```

Here:

- First value goes to `@Department`.
- Second value goes to `@MinimumSalary`.

**Important:** The order matters when using positional parameters.

## Method 2 – Named parameters

We explicitly specify the parameter names.

```sql
EXEC usp_GetEmployeeDetails
    @MinimumSalary = 50000,
    @Department = 'IT';
```

Here, the order does not matter because each value is associated with its parameter name.

### Comparison

| Feature | Positional Parameters | Named Parameters |
|---|---|---|
| Values passed by | Position | Parameter name |
| Order important | Yes | No |
| Readability | Less explicit | More explicit |
| Risk of incorrect mapping | Higher if order is wrong | Lower |

**Recommendation:** Named parameters are easier to read, especially when a procedure has multiple parameters.

---

# 9. Viewing a Stored Procedure Definition

Sometimes we need to check the SQL statements saved inside a stored procedure.

SQL Server provides `sp_helptext`.

### Syntax

```sql
EXEC sp_helptext 'usp_GetAllEmployees';
```

This displays the stored procedure definition in the query results.

### Another method – SSMS Object Explorer

1. Open SQL Server Management Studio.
2. Expand your database.
3. Expand Programmability.
4. Expand Stored Procedures.
5. Right-click the stored procedure.
6. Select Modify to open its definition.

**Note:** The procedure definition may not be available through these methods if it was created with WITH ENCRYPTION or if you lack the necessary permissions.

---

# 10. Modifying a Stored Procedure

We use `ALTER PROCEDURE` to modify an existing stored procedure.

Suppose we initially created a procedure that retrieves all employees.

Now we want to retrieve only IT employees.

### Existing procedure

```sql
CREATE PROCEDURE usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

### Modify the procedure

```sql
ALTER PROCEDURE usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Department = 'IT';
END;
```

### Execute again

```sql
EXEC usp_GetAllEmployees;
```

### Expected output

| EmployeeId | EmployeeName | Department | Salary |
|---:|---|---|---:|
| 101 | Arun | IT | 50000 |
| 104 | Deepa | IT | 55000 |

**Important:** ALTER PROCEDURE modifies the existing procedure. It does not create another procedure.

---

# 11. Dropping a Stored Procedure

We use `DROP PROCEDURE` to delete a stored procedure from the database.

### Syntax

```sql
DROP PROCEDURE ProcedureName;
```

### Example

```sql
DROP PROCEDURE usp_GetAllEmployees;
```

The procedure is now removed from the database.

### Safer syntax

```sql
DROP PROCEDURE IF EXISTS usp_GetAllEmployees;
```

This avoids an error when the procedure does not exist.

**Important:** Dropping a stored procedure does not delete the employee table or its records. It only removes the stored procedure definition.

---

# 12. WITH ENCRYPTION

SQL Server provides the `WITH ENCRYPTION` option when creating or altering a stored procedure.

It prevents the procedure definition from being displayed through normal definition-viewing methods such as `sp_helptext`.

### Example

```sql
CREATE PROCEDURE usp_EncryptedEmployeeReport
WITH ENCRYPTION
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

### Execute the procedure

```sql
EXEC usp_EncryptedEmployeeReport;
```

The procedure can still execute normally.

### Important points

1. WITH ENCRYPTION protects the procedure definition from normal viewing.
2. It does not prevent the procedure from executing.
3. It is not a replacement for database security or access permissions.
4. Keep a secure source-code copy of the procedure before using encryption.
5. Do not assume encryption makes the procedure impossible to recover.

---

# 13. Important Commands Summary

| Requirement | SQL Command |
|---|---|
| Create procedure | CREATE PROCEDURE |
| Short creation syntax | CREATE PROC |
| Execute procedure | EXEC / EXECUTE |
| Modify procedure | ALTER PROCEDURE |
| View definition | sp_helptext |
| Delete procedure | DROP PROCEDURE |
| Encrypt definition | WITH ENCRYPTION |

---

# 14. Hands-on Practice Exercises

Try these exercises before checking the answer key.

## Beginner Level

### Exercise 1
Create a stored procedure named `usp_GetAllEmployees` to retrieve all employees.

### Exercise 2
Execute the procedure using EXEC.

### Exercise 3
Execute the same procedure using EXECUTE.

### Exercise 4
Create a stored procedure named `usp_GetAllDepartments` to retrieve employee names and departments.

### Exercise 5
Create a procedure named `usp_GetEmployeeById` that accepts EmployeeId as an input parameter.

### Exercise 6
Execute the procedure for EmployeeId 103.

## Intermediate Level

### Exercise 7
Create a procedure that accepts Department as a parameter and retrieves employees from that department.

### Exercise 8
Create a procedure that accepts EmployeeId and Department as parameters.

### Exercise 9
Execute a procedure using positional parameters.

### Exercise 10
Execute a procedure using named parameters.

### Exercise 11
Display the definition of `usp_GetAllEmployees` using sp_helptext.

### Exercise 12
Modify `usp_GetAllEmployees` to retrieve only employees from the HR department.

## Real-World Practice

### Exercise 13
Create a procedure named `usp_GetEmployeesByMinimumSalary` that accepts a minimum salary and retrieves employees earning at least that amount.

### Exercise 14
Create a procedure to retrieve employees from a department with a minimum salary condition.

### Exercise 15
Modify an existing procedure using ALTER PROCEDURE.

### Exercise 16
Delete a procedure using DROP PROCEDURE.

### Exercise 17
Create a stored procedure using WITH ENCRYPTION.

### Exercise 18
Explain the difference between CREATE PROCEDURE, ALTER PROCEDURE, and DROP PROCEDURE.

---

# 15. Complete Answer Key

## Answer 1 – Create procedure

```sql
CREATE PROCEDURE usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

## Answer 2 – Execute using EXEC

```sql
EXEC usp_GetAllEmployees;
```

## Answer 3 – Execute using EXECUTE

```sql
EXECUTE usp_GetAllEmployees;
```

## Answer 4 – Retrieve employee names and departments

```sql
CREATE PROCEDURE usp_GetAllDepartments
AS
BEGIN
    SELECT
        EmployeeName,
        Department
    FROM tblEmployee;
END;
```

Execute:

```sql
EXEC usp_GetAllDepartments;
```

## Answer 5 – Procedure with EmployeeId

```sql
CREATE PROCEDURE usp_GetEmployeeById
    @EmployeeId INT
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE EmployeeId = @EmployeeId;
END;
```

## Answer 6 – Execute for EmployeeId 103

```sql
EXEC usp_GetEmployeeById 103;
```

Expected employee: Charan.

## Answer 7 – Procedure with Department parameter

```sql
CREATE PROCEDURE usp_GetEmployeesByDepartment
    @Department VARCHAR(50)
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Department = @Department;
END;
```

Execute:

```sql
EXEC usp_GetEmployeesByDepartment 'IT';
```

## Answer 8 – Procedure with two parameters

```sql
CREATE PROCEDURE usp_GetEmployeeByIdAndDepartment
    @EmployeeId INT,
    @Department VARCHAR(50)
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE EmployeeId = @EmployeeId
      AND Department = @Department;
END;
```

## Answer 9 – Positional parameters

```sql
EXEC usp_GetEmployeeByIdAndDepartment 101, 'IT';
```

## Answer 10 – Named parameters

```sql
EXEC usp_GetEmployeeByIdAndDepartment
    @Department = 'IT',
    @EmployeeId = 101;
```

## Answer 11 – View procedure definition

```sql
EXEC sp_helptext 'usp_GetAllEmployees';
```

## Answer 12 – Modify procedure

```sql
ALTER PROCEDURE usp_GetAllEmployees
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Department = 'HR';
END;
```

Execute:

```sql
EXEC usp_GetAllEmployees;
```

Expected employee: Bala.

## Answer 13 – Minimum salary procedure

```sql
CREATE PROCEDURE usp_GetEmployeesByMinimumSalary
    @MinimumSalary DECIMAL(10,2)
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Salary >= @MinimumSalary;
END;
```

Execute:

```sql
EXEC usp_GetEmployeesByMinimumSalary 50000;
```

Expected employees: Arun, Charan, Deepa.

## Answer 14 – Department and minimum salary

```sql
CREATE PROCEDURE usp_GetEmployeesByDepartmentAndSalary
    @Department VARCHAR(50),
    @MinimumSalary DECIMAL(10,2)
AS
BEGIN
    SELECT *
    FROM tblEmployee
    WHERE Department = @Department
      AND Salary >= @MinimumSalary;
END;
```

Execute:

```sql
EXEC usp_GetEmployeesByDepartmentAndSalary 'IT', 50000;
```

Expected employees: Arun, Deepa.

## Answer 15 – ALTER PROCEDURE

```sql
ALTER PROCEDURE usp_GetEmployeesByMinimumSalary
    @MinimumSalary DECIMAL(10,2)
AS
BEGIN
    SELECT
        EmployeeId,
        EmployeeName,
        Salary
    FROM tblEmployee
    WHERE Salary >= @MinimumSalary;
END;
```

## Answer 16 – DROP PROCEDURE

```sql
DROP PROCEDURE IF EXISTS usp_GetEmployeesByMinimumSalary;
```

## Answer 17 – WITH ENCRYPTION

```sql
CREATE PROCEDURE usp_EncryptedEmployeeReport
WITH ENCRYPTION
AS
BEGIN
    SELECT *
    FROM tblEmployee;
END;
```

Execute:

```sql
EXEC usp_EncryptedEmployeeReport;
```

## Answer 18 – CREATE vs ALTER vs DROP

| Command | Purpose |
|---|---|
| CREATE PROCEDURE | Creates a new stored procedure |
| ALTER PROCEDURE | Modifies an existing stored procedure |
| DROP PROCEDURE | Deletes a stored procedure |

---

# 16. Revision Questions

1. What is a stored procedure?
2. Why do we use stored procedures?
3. Which command creates a stored procedure?
4. How do we execute a stored procedure?
5. What is the purpose of input parameters?
6. What is the difference between positional and named parameters?
7. How do we view a stored procedure definition?
8. What does ALTER PROCEDURE do?
9. What does DROP PROCEDURE do?
10. What is WITH ENCRYPTION?
11. Why should we avoid using sp_ as a prefix for user-defined procedures?
12. Does dropping a stored procedure delete the underlying table?

---

# 17. Completion Checklist

- [ ] I understand what a stored procedure is.
- [ ] I can create a stored procedure.
- [ ] I can execute a stored procedure.
- [ ] I can create a procedure with input parameters.
- [ ] I understand positional parameters.
- [ ] I understand named parameters.
- [ ] I can view a procedure definition.
- [ ] I can modify a procedure.
- [ ] I can delete a procedure.
- [ ] I understand WITH ENCRYPTION.
- [ ] I completed all 18 exercises.

---

# 18. Microsoft Documentation

- [CREATE PROCEDURE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-procedure-transact-sql)
- [ALTER PROCEDURE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-procedure-transact-sql)
- [DROP PROCEDURE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/drop-procedure-transact-sql)
- [EXECUTE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql)

---

# Final Practice Recommendation

1. Create the sample employee table.
2. Create a procedure to retrieve all employees.
3. Execute it using EXEC.
4. Create a procedure with one input parameter.
5. Practise positional and named parameters.
6. Modify the procedure using ALTER.
7. View the procedure using sp_helptext.
8. Drop the procedure using DROP.
9. Complete all 18 exercises.

**Key takeaway:** A stored procedure is a reusable group of SQL statements saved in the database. We create it once and execute it whenever required.
