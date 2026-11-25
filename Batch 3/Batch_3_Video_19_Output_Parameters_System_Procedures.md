
# Batch 3 – Video 19: OUTPUT Parameters and System Stored Procedures

## 1. Learning Objectives

By the end of this lesson, you will understand:

- Difference between input and output parameters.
- How to create a stored procedure with OUTPUT parameters.
- How to assign values to OUTPUT parameters.
- How to execute stored procedures with OUTPUT parameters.
- How to receive output values using SQL variables.
- How to pass named parameters.
- How to use sp_help.
- How to use sp_helptext.
- How to use sp_depends.

---

# 2. Understanding Input and Output Parameters

In the previous lesson, we learned how to pass input parameters to stored procedures.

An input parameter sends a value into a stored procedure.

An output parameter sends a value from a stored procedure back to the calling SQL statement.

### Input parameter example

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

Execution:

```sql
EXEC usp_GetEmployeeById 101;
```

Here, EmployeeId is passed into the procedure.

### Output parameter example

Suppose we want to calculate the total number of employees and return that count to the calling SQL statement.

We can use an OUTPUT parameter.

```sql
@EmployeeCount INT OUTPUT
```

The procedure calculates the count and sends it back through this parameter.

### Difference between input and output parameters

| Input Parameter | Output Parameter |
|---|---|
| Sends a value into the procedure | Sends a value back from the procedure |
| Used to provide input | Used to return a value |
| Declared normally | Declared using OUTPUT or OUT |
| Example: @Gender VARCHAR(10) | Example: @EmployeeCount INT OUTPUT |

---

# 3. Create Sample Employee Table

We will use a simple employee table throughout this lesson.

### Create table

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    Gender VARCHAR(10),
    Department VARCHAR(50),
    Salary DECIMAL(10,2)
);
```

### Insert sample records

```sql
INSERT INTO tblEmployee
VALUES
    (101, 'Arun', 'Male', 'IT', 50000),
    (102, 'Bala', 'Male', 'HR', 45000),
    (103, 'Charan', 'Male', 'Finance', 60000),
    (104, 'Deepa', 'Female', 'IT', 55000),
    (105, 'Esha', 'Female', 'Sales', 40000),
    (106, 'Farhan', 'Male', 'IT', 52000),
    (107, 'Gita', 'Female', 'HR', 48000),
    (108, 'Hari', 'Male', 'Finance', 65000);
```

### View the records

```sql
SELECT *
FROM tblEmployee;
```

### Expected output

| EmployeeId | EmployeeName | Gender | Department | Salary |
|---:|---|---|---|---:|
| 101 | Arun | Male | IT | 50000 |
| 102 | Bala | Male | HR | 45000 |
| 103 | Charan | Male | Finance | 60000 |
| 104 | Deepa | Female | IT | 55000 |
| 105 | Esha | Female | Sales | 40000 |
| 106 | Farhan | Male | IT | 52000 |
| 107 | Gita | Female | HR | 48000 |
| 108 | Hari | Male | Finance | 65000 |

---

# 4. Creating a Stored Procedure with OUTPUT Parameter

### Requirement

Create a stored procedure that:

1. Accepts Gender as an input parameter.
2. Calculates the number of employees belonging to that gender.
3. Returns the count using an OUTPUT parameter.

### Create procedure

```sql
CREATE PROCEDURE usp_GetEmployeeCountByGender
    @Gender VARCHAR(10),
    @EmployeeCount INT OUTPUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Gender = @Gender;
END;
```

### Understanding the procedure

| Parameter | Type | Purpose |
|---|---|---|
| @Gender | Input | Receives the gender value |
| @EmployeeCount | Output | Returns the employee count |

### Important statement

```sql
SELECT @EmployeeCount = COUNT(*)
FROM tblEmployee
WHERE Gender = @Gender;
```

This statement calculates the count and assigns it to the output parameter.

It does not return a normal SELECT result set.

---

# 5. Executing a Procedure with OUTPUT Parameter

To receive an output parameter, we need to:

1. Declare a variable.
2. Execute the stored procedure.
3. Pass the variable to the output parameter.
4. Specify OUTPUT during execution.
5. Display the returned value.

### Step 1 – Declare a variable

```sql
DECLARE @TotalCount INT;
```

### Step 2 – Execute the procedure

```sql
EXEC usp_GetEmployeeCountByGender
    'Male',
    @TotalCount OUTPUT;
```

### Step 3 – Display the result

```sql
SELECT @TotalCount AS EmployeeCount;
```

### Complete execution

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    'Male',
    @TotalCount OUTPUT;

SELECT @TotalCount AS EmployeeCount;
```

### Expected output

| EmployeeCount |
|---:|
| 5 |

There are five male employees in our sample table.

---

# 6. What Happens If We Omit OUTPUT?

The OUTPUT keyword is important when executing a procedure.

Consider this example:

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    'Male',
    @TotalCount;

SELECT @TotalCount AS EmployeeCount;
```

The procedure executes, but the caller's variable does not receive the output value.

If the variable was initially NULL, it remains NULL.

### Expected output

| EmployeeCount |
|---|
| NULL |

### Correct execution

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    'Male',
    @TotalCount OUTPUT;

SELECT @TotalCount AS EmployeeCount;
```

Expected result: 5.

**Remember:** The OUTPUT keyword must be specified when executing the procedure to receive the returned value.

---

# 7. Using OUT Instead of OUTPUT

SQL Server supports both OUT and OUTPUT.

They are interchangeable.

### Procedure using OUT

```sql
CREATE PROCEDURE usp_GetEmployeeCount
    @Gender VARCHAR(10),
    @EmployeeCount INT OUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Gender = @Gender;
END;
```

### Execute

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCount
    'Female',
    @TotalCount OUT;

SELECT @TotalCount AS EmployeeCount;
```

Expected output:

| EmployeeCount |
|---:|
| 3 |

---

# 8. Passing Named Parameters

In the previous lesson, we learned about positional and named parameters.

The same concept applies to OUTPUT parameters.

### Positional parameter example

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    'Male',
    @TotalCount OUTPUT;

SELECT @TotalCount AS EmployeeCount;
```

The values must follow the order in which the parameters are declared.

### Named parameter example

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    @EmployeeCount = @TotalCount OUTPUT,
    @Gender = 'Male';

SELECT @TotalCount AS EmployeeCount;
```

Here, we can pass parameters in a different order because we specify their names.

### Comparison

| Positional Parameters | Named Parameters |
|---|---|
| Values passed based on order | Values passed using parameter names |
| Order matters | Order does not matter |
| Shorter syntax | More readable |
| Easier to make mapping mistakes | Easier to identify parameters |

**Recommendation:** Prefer named parameters when a procedure contains multiple parameters.

---

# 9. System Stored Procedure – sp_help

## What is sp_help?

sp_help is a SQL Server system stored procedure used to display information about database objects.

It can provide details about:

- Tables
- Stored procedures
- Columns
- Data types
- Constraints
- Indexes
- Procedure parameters

### Syntax

```sql
EXEC sp_help 'ObjectName';
```

### Example 1 – Get table information

```sql
EXEC sp_help 'tblEmployee';
```

It displays information about the table, including its columns and data types.

### Example 2 – Get stored procedure information

```sql
EXEC sp_help 'usp_GetEmployeeCountByGender';
```

It displays information about the stored procedure and its parameters.

### Example 3 – Get information about a column

```sql
EXEC sp_help 'tblEmployee.EmployeeName';
```

This provides information about the specified column.

### SSMS shortcut – Alt + F1

You can also use the SSMS shortcut:

1. Open a query window.
2. Type the table or object name.
3. Select the object name.
4. Press Alt + F1.

Example:

```sql
tblEmployee
```

Select the name and press Alt + F1 to retrieve its information.

---

# 10. System Stored Procedure – sp_helptext

## What is sp_helptext?

sp_helptext displays the SQL definition of a stored procedure or another supported SQL object.

It is useful when we want to understand the SQL statements saved inside an existing procedure.

### Syntax

```sql
EXEC sp_helptext 'ProcedureName';
```

### Example

```sql
EXEC sp_helptext 'usp_GetEmployeeCountByGender';
```

SQL Server displays the procedure definition.

### Expected definition

```sql
CREATE PROCEDURE usp_GetEmployeeCountByGender
    @Gender VARCHAR(10),
    @EmployeeCount INT OUTPUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Gender = @Gender;
END;
```

### Important points

1. sp_helptext displays the stored definition.
2. It is useful for understanding existing procedures.
3. It can also be used with supported views and other SQL objects.
4. It cannot display the definition of an encrypted procedure through this method.

### Example with encrypted procedure

```sql
CREATE PROCEDURE usp_EncryptedEmployeeCount
WITH ENCRYPTION
AS
BEGIN
    SELECT COUNT(*) AS EmployeeCount
    FROM tblEmployee;
END;
```

Execute:

```sql
EXEC sp_helptext 'usp_EncryptedEmployeeCount';
```

The procedure definition is not available through sp_helptext because it is encrypted.

---

# 11. System Stored Procedure – sp_depends

## What is sp_depends?

sp_depends is used to display dependencies between database objects.

For example:

- Which stored procedures reference a table?
- Which database objects depend on another object?
- Which objects might be affected when a table is modified?

### Example 1 – Check table dependencies

```sql
EXEC sp_depends 'tblEmployee';
```

This displays objects that depend on or are referenced by the specified table, subject to SQL Server's dependency tracking.

### Example 2 – Check stored procedure dependencies

```sql
EXEC sp_depends 'usp_GetEmployeeCountByGender';
```

This checks dependency information for the stored procedure.

### Why is this useful?

Suppose we want to drop or rename a table.

Before changing the table, we should identify stored procedures or other objects that may depend on it.

Dependency information helps us understand the potential impact.

### Important note

sp_depends is deprecated in modern SQL Server versions.

For current development, Microsoft recommends using catalog views such as:

- sys.sql_expression_dependencies
- sys.dm_sql_referencing_entities
- sys.dm_sql_referenced_entities

For example:

```sql
SELECT *
FROM sys.sql_expression_dependencies
WHERE referenced_entity_name = 'tblEmployee';
```

This provides dependency information recorded by SQL Server.

**For this lesson, understand sp_depends because it is covered in the tutorial.**

---

# 12. Comparison of System Stored Procedures

| Procedure | Purpose |
|---|---|
| sp_help | Displays information about database objects |
| sp_helptext | Displays the definition of supported SQL objects |
| sp_depends | Displays object dependency information |

### Quick examples

```sql
EXEC sp_help 'tblEmployee';

EXEC sp_helptext 'usp_GetEmployeeCountByGender';

EXEC sp_depends 'tblEmployee';
```

---

# 13. Hands-on Practice Exercises

Try solving these questions before checking the answers.

## Beginner Level

### Exercise 1
Create a stored procedure that accepts Gender as an input parameter and returns the employee count using an OUTPUT parameter.

### Exercise 2
Execute the procedure for Male employees.

### Exercise 3
Execute the procedure for Female employees.

### Exercise 4
Display the output count using a SELECT statement.

### Exercise 5
Execute the procedure without specifying OUTPUT. Observe the result.

### Exercise 6
Create the same procedure using OUT instead of OUTPUT.

## Intermediate Level

### Exercise 7
Create a procedure that accepts Department as input and returns the number of employees in that department.

### Exercise 8
Create a procedure that accepts Gender and returns the total salary of employees belonging to that gender through an OUTPUT parameter.

### Exercise 9
Execute a stored procedure using positional parameters.

### Exercise 10
Execute the same procedure using named parameters in a different order.

### Exercise 11
Use sp_help to display the structure of tblEmployee.

### Exercise 12
Use sp_help to display the details of usp_GetEmployeeCountByGender.

## Real-World Practice

### Exercise 13
Create a procedure that accepts Department and returns the number of employees through an OUTPUT parameter.

### Exercise 14
Create a procedure that accepts Department and returns the average salary through an OUTPUT parameter.

### Exercise 15
Use sp_helptext to display the definition of an existing stored procedure.

### Exercise 16
Use sp_depends to check dependencies for tblEmployee.

### Exercise 17
Create an encrypted stored procedure and try viewing its definition using sp_helptext.

### Exercise 18
Explain the difference between sp_help, sp_helptext, and sp_depends.

---

# 14. Complete Answer Key

## Answer 1 – Employee count by gender

```sql
CREATE PROCEDURE usp_GetEmployeeCountByGender
    @Gender VARCHAR(10),
    @EmployeeCount INT OUTPUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Gender = @Gender;
END;
```

## Answer 2 – Male employee count

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    @Gender = 'Male',
    @EmployeeCount = @TotalCount OUTPUT;

SELECT @TotalCount AS MaleEmployeeCount;
```

Expected output: 5.

## Answer 3 – Female employee count

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender
    @Gender = 'Female',
    @EmployeeCount = @TotalCount OUTPUT;

SELECT @TotalCount AS FemaleEmployeeCount;
```

Expected output: 3.

## Answer 4 – Display output count

```sql
DECLARE @EmployeeCount INT;

EXEC usp_GetEmployeeCountByGender
    'Male',
    @EmployeeCount OUTPUT;

SELECT @EmployeeCount AS EmployeeCount;
```

## Answer 5 – Execute without OUTPUT

```sql
DECLARE @EmployeeCount INT;

EXEC usp_GetEmployeeCountByGender
    'Male',
    @EmployeeCount;

SELECT @EmployeeCount AS EmployeeCount;
```

If the variable starts as NULL, it remains NULL because the caller did not specify OUTPUT.

## Answer 6 – Use OUT

```sql
CREATE PROCEDURE usp_GetEmployeeCountByGender_OUT
    @Gender VARCHAR(10),
    @EmployeeCount INT OUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Gender = @Gender;
END;
```

Execute:

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByGender_OUT
    'Female',
    @TotalCount OUT;

SELECT @TotalCount AS EmployeeCount;
```

Expected output: 3.

## Answer 7 – Employee count by department

```sql
CREATE PROCEDURE usp_GetEmployeeCountByDepartment
    @Department VARCHAR(50),
    @EmployeeCount INT OUTPUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Department = @Department;
END;
```

Execute:

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByDepartment
    'IT',
    @TotalCount OUTPUT;

SELECT @TotalCount AS EmployeeCount;
```

Expected output: 3.

## Answer 8 – Total salary by gender

```sql
CREATE PROCEDURE usp_GetTotalSalaryByGender
    @Gender VARCHAR(10),
    @TotalSalary DECIMAL(18,2) OUTPUT
AS
BEGIN
    SELECT @TotalSalary = SUM(Salary)
    FROM tblEmployee
    WHERE Gender = @Gender;
END;
```

Execute:

```sql
DECLARE @Salary DECIMAL(18,2);

EXEC usp_GetTotalSalaryByGender
    'Male',
    @Salary OUTPUT;

SELECT @Salary AS TotalSalary;
```

Expected output: 267000.

## Answer 9 – Positional parameters

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByDepartment
    'HR',
    @TotalCount OUTPUT;

SELECT @TotalCount AS EmployeeCount;
```

Expected output: 2.

## Answer 10 – Named parameters in a different order

```sql
DECLARE @TotalCount INT;

EXEC usp_GetEmployeeCountByDepartment
    @EmployeeCount = @TotalCount OUTPUT,
    @Department = 'IT';

SELECT @TotalCount AS EmployeeCount;
```

Expected output: 3.

## Answer 11 – sp_help for table

```sql
EXEC sp_help 'tblEmployee';
```

## Answer 12 – sp_help for procedure

```sql
EXEC sp_help 'usp_GetEmployeeCountByGender';
```

## Answer 13 – Employee count by department

```sql
CREATE PROCEDURE usp_GetDepartmentEmployeeCount
    @Department VARCHAR(50),
    @EmployeeCount INT OUTPUT
AS
BEGIN
    SELECT @EmployeeCount = COUNT(*)
    FROM tblEmployee
    WHERE Department = @Department;
END;
```

## Answer 14 – Average salary by department

```sql
CREATE PROCEDURE usp_GetAverageSalaryByDepartment
    @Department VARCHAR(50),
    @AverageSalary DECIMAL(18,2) OUTPUT
AS
BEGIN
    SELECT @AverageSalary = AVG(Salary)
    FROM tblEmployee
    WHERE Department = @Department;
END;
```

Execute:

```sql
DECLARE @AverageSalary DECIMAL(18,2);

EXEC usp_GetAverageSalaryByDepartment
    'IT',
    @AverageSalary OUTPUT;

SELECT @AverageSalary AS AverageSalary;
```

Expected output: 52333.33.

## Answer 15 – View procedure definition

```sql
EXEC sp_helptext 'usp_GetEmployeeCountByGender';
```

## Answer 16 – Check dependencies

```sql
EXEC sp_depends 'tblEmployee';
```

Note: sp_depends is deprecated. For modern SQL Server dependency analysis, use supported catalog views and dependency functions.

## Answer 17 – Encrypted procedure

```sql
CREATE PROCEDURE usp_EncryptedEmployeeCount
WITH ENCRYPTION
AS
BEGIN
    SELECT COUNT(*) AS EmployeeCount
    FROM tblEmployee;
END;
```

Execute:

```sql
EXEC usp_EncryptedEmployeeCount;
```

Try:

```sql
EXEC sp_helptext 'usp_EncryptedEmployeeCount';
```

The definition is not displayed through sp_helptext because the procedure is encrypted.

## Answer 18 – System stored procedure comparison

| Procedure | Purpose |
|---|---|
| sp_help | Displays object details |
| sp_helptext | Displays SQL definition |
| sp_depends | Displays dependency information |

---

# 15. Revision Questions

1. What is an OUTPUT parameter?
2. What is the difference between an input and an output parameter?
3. How do we declare an OUTPUT parameter?
4. How do we assign a value to an OUTPUT parameter?
5. Why do we declare a variable before executing a procedure with an OUTPUT parameter?
6. Why must OUTPUT be specified during execution?
7. What is the difference between OUT and OUTPUT?
8. What is the difference between positional and named parameters?
9. What is sp_help?
10. What is sp_helptext?
11. What is sp_depends?
12. Why should we check object dependencies before dropping a table?
13. Is sp_depends recommended for new SQL Server development?

---

# 16. Completion Checklist

- [ ] I understand input parameters.
- [ ] I understand output parameters.
- [ ] I can create a stored procedure with OUTPUT.
- [ ] I can assign values to OUTPUT parameters.
- [ ] I can declare a variable to receive output.
- [ ] I can execute a procedure using OUTPUT.
- [ ] I understand what happens when OUTPUT is omitted.
- [ ] I can pass named parameters.
- [ ] I can use sp_help.
- [ ] I can use sp_helptext.
- [ ] I understand sp_depends.
- [ ] I know that sp_depends is deprecated.
- [ ] I completed all 18 exercises.

---

# 17. Microsoft Documentation

- [CREATE PROCEDURE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-procedure-transact-sql)
- [EXECUTE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql)
- [sp_help (Transact-SQL)](https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-help-transact-sql)
- [sp_helptext (Transact-SQL)](https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-helptext-transact-sql)
- [sys.sql_expression_dependencies](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql)
- [sys.dm_sql_referencing_entities](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql)

---

# Final Practice Recommendation

1. Create the employee table.
2. Create a procedure with Gender as an input parameter.
3. Add EmployeeCount as an OUTPUT parameter.
4. Execute the procedure for Male employees.
5. Execute it again for Female employees.
6. Try executing without OUTPUT and observe the difference.
7. Practise positional and named parameter execution.
8. Explore the employee table using sp_help.
9. View a procedure definition using sp_helptext.
10. Explore dependencies using sp_depends.

**Key takeaway:** An OUTPUT parameter allows a stored procedure to return a value to the calling SQL statement. System stored procedures such as sp_help, sp_helptext, and sp_depends help us inspect and understand database objects.
