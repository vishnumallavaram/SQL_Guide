
# Batch 1 – Video 9: UNIQUE Key Constraint in SQL Server

## 1. Learning Objectives

By completing this practice, you will understand:

1. What a UNIQUE constraint is.
2. Why we use UNIQUE constraints.
3. How to create a UNIQUE constraint using SSMS Designer.
4. How to create a UNIQUE constraint using SQL queries.
5. The difference between PRIMARY KEY and UNIQUE KEY.
6. How to enforce uniqueness across multiple columns.
7. How SQL Server handles NULL values in UNIQUE constraints.
8. How to drop a UNIQUE constraint.

---

## 2. What Is a UNIQUE Constraint?

A UNIQUE constraint ensures that duplicate values are not allowed in a column or combination of columns.

For example, consider an employee table.

Every employee should have a different email address.

If two employees have the same email address, the database should reject the second record.

This is where we use a UNIQUE constraint.

### Example

```sql
CREATE TABLE tblPerson
(
    PersonId INT PRIMARY KEY,
    Name NVARCHAR(50),
    Email NVARCHAR(100) UNIQUE
);
```

In this example:

- PersonId is the Primary Key.
- Email has a UNIQUE constraint.
- Duplicate email addresses are not allowed.

---

## 3. Create a UNIQUE Constraint Using SQL Queries

General syntax:

```sql
ALTER TABLE Table_Name
ADD CONSTRAINT Constraint_Name
UNIQUE (Column_Name);
```

### Practical Example

First, create a table without a UNIQUE constraint.

```sql
CREATE TABLE tblPerson
(
    PersonId INT PRIMARY KEY,
    Name NVARCHAR(50),
    Email NVARCHAR(100)
);
```

Now add a UNIQUE constraint to Email.

```sql
ALTER TABLE tblPerson
ADD CONSTRAINT UQ_tblPerson_Email
UNIQUE (Email);
```

Verify the constraint by inserting records.

```sql
INSERT INTO tblPerson
VALUES (1, 'Sam', 'sam@gmail.com');

INSERT INTO tblPerson
VALUES (2, 'Sara', 'sara@gmail.com');

SELECT * FROM tblPerson;
```

Expected output:

| PersonId | Name | Email |
|---:|---|---|
| 1 | Sam | sam@gmail.com |
| 2 | Sara | sara@gmail.com |

---

## 4. Test Duplicate Values

Now try inserting another person with an existing email address.

```sql
INSERT INTO tblPerson
VALUES (3, 'John', 'sam@gmail.com');
```

Expected result:

SQL Server returns a UNIQUE constraint violation error because `sam@gmail.com` already exists.

**Observation:** A UNIQUE constraint prevents duplicate values from being inserted.

Try updating an existing record with another person's email address.

```sql
UPDATE tblPerson
SET Email = 'sam@gmail.com'
WHERE PersonId = 2;
```

This update should also fail because the email address already belongs to another record.

**Important:** UNIQUE constraints apply to both INSERT and UPDATE operations.

---

## 5. Create a UNIQUE Constraint Using SSMS Designer

You can also create a UNIQUE constraint using SQL Server Management Studio.

Follow these steps:

1. Right-click the table and select Design.
2. Right-click the column you want to make unique.
3. Select Indexes/Keys.
4. Click Add.
5. Select the required column under Columns.
6. Change Type to Unique Key.
7. Close the dialog.
8. Save the table.

**Practice task:**

Create a table called `tblCustomer` with:

- CustomerId – INT, Primary Key
- CustomerName – NVARCHAR(50)
- Email – NVARCHAR(100)

Using SSMS Designer, make Email unique.

Then try inserting two customers with the same email address.

Observe the result.

---

## 6. Difference Between PRIMARY KEY and UNIQUE Constraint

Both PRIMARY KEY and UNIQUE constraints enforce uniqueness.

However, there are important differences.

| Feature | PRIMARY KEY | UNIQUE Constraint |
|---|---|---|
| Purpose | Uniquely identifies each record | Prevents duplicate values |
| Number per table | Only one | Multiple allowed |
| Allows NULL | No | Allows one NULL for a single-column constraint |
| Duplicate values | Not allowed | Not allowed |
| Can contain multiple columns | Yes | Yes |
| Automatically creates an index | Yes | Yes |

### Example: Multiple UNIQUE Constraints

A table can have only one PRIMARY KEY, but it can have multiple UNIQUE constraints.

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName NVARCHAR(50),
    Email NVARCHAR(100),
    PhoneNumber VARCHAR(15),

    CONSTRAINT UQ_tblEmployee_Email
    UNIQUE (Email),

    CONSTRAINT UQ_tblEmployee_Phone
    UNIQUE (PhoneNumber)
);
```

In this example:

- EmployeeId is the Primary Key.
- Email must be unique.
- PhoneNumber must also be unique.

Both Email and PhoneNumber are independently unique.

---

## 7. Understanding NULL in UNIQUE Constraints

This is an important concept.

In SQL Server, a single-column UNIQUE constraint allows only one NULL value.

Let's test it.

Create a table:

```sql
CREATE TABLE tblStudent
(
    StudentId INT PRIMARY KEY,
    StudentName NVARCHAR(50),
    Email NVARCHAR(100),

    CONSTRAINT UQ_tblStudent_Email
    UNIQUE (Email)
);
```

Insert records:

```sql
INSERT INTO tblStudent
VALUES (1, 'Ravi', 'ravi@gmail.com');

INSERT INTO tblStudent
VALUES (2, 'Kiran', NULL);
```

Both inserts should succeed.

Now try inserting another NULL:

```sql
INSERT INTO tblStudent
VALUES (3, 'Priya', NULL);
```

Expected result:

SQL Server rejects the insert because the UNIQUE constraint already contains a NULL value.

**Important:**

- A single-column UNIQUE constraint allows one NULL.
- A PRIMARY KEY does not allow NULL.
- If a column must always have a value, use NOT NULL along with UNIQUE.

Example:

```sql
CREATE TABLE tblCustomer
(
    CustomerId INT PRIMARY KEY,
    CustomerName NVARCHAR(50),
    Email NVARCHAR(100) NOT NULL UNIQUE
);
```

Now Email must be provided and must be unique.

---

## 8. UNIQUE Constraint on Multiple Columns

Sometimes, uniqueness is required based on a combination of columns rather than one individual column.

Example:

A product may belong to multiple categories, but the same product should not be registered twice within the same category.

We can use a composite UNIQUE constraint.

### Create Table

```sql
CREATE TABLE tblProduct
(
    ProductId INT PRIMARY KEY,
    ProductName NVARCHAR(50),
    Category NVARCHAR(50),
    ProductCode NVARCHAR(20),

    CONSTRAINT UQ_tblProduct_Code_Category
    UNIQUE (ProductCode, Category)
);
```

### Insert Valid Records

```sql
INSERT INTO tblProduct
VALUES (1, 'Laptop', 'Electronics', 'P100');

INSERT INTO tblProduct
VALUES (2, 'Laptop', 'Accessories', 'P100');

INSERT INTO tblProduct
VALUES (3, 'Mouse', 'Electronics', 'P101');
```

These inserts should succeed.

Why?

Because the combination of ProductCode and Category is unique.

### Test Duplicate Combination

```sql
INSERT INTO tblProduct
VALUES (4, 'Laptop', 'Electronics', 'P100');
```

Expected result:

SQL Server rejects this record because the combination:

- ProductCode = P100
- Category = Electronics

already exists.

### Important Observation

The following combinations are different:

| ProductCode | Category |
|---|---|
| P100 | Electronics |
| P100 | Accessories |

Therefore, both records are allowed.

**Remember:** A composite UNIQUE constraint checks the combination of all specified columns, not each column independently.

---

## 9. Drop a UNIQUE Constraint

Sometimes business requirements change, and we may need to remove a UNIQUE constraint.

General syntax:

```sql
ALTER TABLE Table_Name
DROP CONSTRAINT Constraint_Name;
```

Example:

```sql
ALTER TABLE tblPerson
DROP CONSTRAINT UQ_tblPerson_Email;
```

Now the Email column no longer has that UNIQUE constraint.

Test it:

```sql
INSERT INTO tblPerson
VALUES (3, 'John', 'sam@gmail.com');

SELECT * FROM tblPerson;
```

This insert should succeed if no other constraint prevents it.

**Important:** Dropping a UNIQUE constraint does not delete the column or existing records. It only removes the uniqueness rule.

---

## 10. Practice Scenarios

Complete these exercises independently.

### Scenario 1: Employee Registration

Create `tblEmployee` with:

- EmployeeId – INT, Primary Key
- EmployeeName – NVARCHAR(50)
- Email – NVARCHAR(100)
- PhoneNumber – VARCHAR(15)

Tasks:

1. Create a UNIQUE constraint on Email.
2. Create another UNIQUE constraint on PhoneNumber.
3. Insert five employees.
4. Try inserting an existing email address.
5. Try inserting an existing phone number.
6. Try updating an employee with another employee's email.
7. Explain the results.

### Scenario 2: Student Registration

Create `tblStudent` with:

- StudentId – INT, Primary Key
- StudentName – NVARCHAR(50)
- Email – NVARCHAR(100)

Tasks:

1. Add a UNIQUE constraint to Email.
2. Insert one student with NULL email.
3. Insert another student with NULL email.
4. Observe the result.
5. Modify the Email column to NOT NULL.
6. Try inserting a NULL value again.

### Scenario 3: Product Registration

Create `tblProduct` with:

- ProductId – INT, Primary Key
- ProductName – NVARCHAR(50)
- Category – NVARCHAR(50)
- ProductCode – NVARCHAR(20)

Tasks:

1. Add a composite UNIQUE constraint on ProductCode and Category.
2. Insert products with the same ProductCode but different categories.
3. Insert the same ProductCode and Category combination again.
4. Observe the result.
5. Explain why some records are accepted and others are rejected.

### Scenario 4: Multiple UNIQUE Constraints

Create `tblCustomer` with:

- CustomerId – INT, Primary Key
- CustomerName – NVARCHAR(50)
- Email – NVARCHAR(100)
- PhoneNumber – VARCHAR(15)
- AadhaarNumber – VARCHAR(12)

Tasks:

1. Add separate UNIQUE constraints to Email, PhoneNumber and AadhaarNumber.
2. Insert valid customer records.
3. Test duplicate values for each unique column.
4. Drop the PhoneNumber UNIQUE constraint.
5. Try inserting duplicate phone numbers.
6. Explain the result.

---

## 11. Interview Questions

1. What is a UNIQUE constraint?
2. What is the difference between PRIMARY KEY and UNIQUE constraint?
3. Can a table have multiple UNIQUE constraints?
4. Can a table have multiple PRIMARY KEY constraints?
5. Can a UNIQUE constraint contain NULL values?
6. How many NULL values are allowed in a single-column UNIQUE constraint in SQL Server?
7. What is a composite UNIQUE constraint?
8. Can we create a UNIQUE constraint on two or more columns?
9. What happens when we insert duplicate values into a UNIQUE column?
10. Can we update a column that has a UNIQUE constraint?
11. How do you drop a UNIQUE constraint?
12. What is the difference between UNIQUE and NOT NULL?
13. Can a UNIQUE constraint be applied to an existing table?
14. What happens if existing duplicate values are present when adding a UNIQUE constraint?

---

## 12. Completion Checklist

- [ ] Understand the purpose of a UNIQUE constraint.
- [ ] Create a UNIQUE constraint using SQL queries.
- [ ] Create a UNIQUE constraint using SSMS Designer.
- [ ] Test duplicate INSERT operations.
- [ ] Test duplicate UPDATE operations.
- [ ] Understand PRIMARY KEY versus UNIQUE.
- [ ] Test NULL handling.
- [ ] Create a composite UNIQUE constraint.
- [ ] Create multiple UNIQUE constraints on one table.
- [ ] Drop a UNIQUE constraint.
- [ ] Complete all four practice scenarios.
- [ ] Answer the interview questions.

---

## 13. Official Microsoft Documentation

1. [Create UNIQUE Constraints](https://learn.microsoft.com/en-us/sql/relational-databases/tables/create-unique-constraints)
2. [ALTER TABLE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql)
3. [PRIMARY KEY and UNIQUE Constraints](https://learn.microsoft.com/en-us/sql/relational-databases/tables/primary-and-foreign-key-constraints)

---

## 14. Important Learning Notes

1. PRIMARY KEY uniquely identifies a record.
2. A table can have only one PRIMARY KEY.
3. A table can have multiple UNIQUE constraints.
4. A single-column UNIQUE constraint allows only one NULL in SQL Server.
5. A composite UNIQUE constraint enforces uniqueness on the combination of columns.
6. UNIQUE constraints apply to both INSERT and UPDATE operations.
7. Dropping a UNIQUE constraint removes the uniqueness rule but does not delete existing data.

**Real-world application:**

UNIQUE constraints are commonly used for:

- Employee email addresses.
- Customer email addresses.
- Mobile numbers.
- Product codes.
- Bank account numbers.
- Usernames.

They help maintain data quality and prevent accidental duplicate records.
