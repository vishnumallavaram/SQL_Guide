
# Batch 1 — Video 6: CHECK Constraint in SQL Server

## 1. Learning Objectives

By the end of this lesson, students should be able to:

- Understand what a CHECK constraint is.
- Explain why CHECK constraints are required.
- Create CHECK constraints while creating a table.
- Add CHECK constraints to existing tables.
- Validate data during INSERT and UPDATE operations.
- Understand how NULL behaves with CHECK constraints.
- Use multiple conditions in a CHECK constraint.
- Drop an existing CHECK constraint.
- Apply CHECK constraints to real-world scenarios.

---

## 2. What is a CHECK Constraint?

A CHECK constraint is used to restrict the values that can be inserted or updated in a table.

It ensures that data follows predefined business rules.

### Example

Imagine we have a table named `tblPerson` with an `Age` column.

The Age column is an INTEGER data type.

An INTEGER column can accept:
- Negative values.
- Zero.
- Values greater than 150.
- Any other value within the integer range.

However, according to our business rule, a person's age should be between 1 and 149.

We can use a CHECK constraint to enforce this rule.

```sql
ALTER TABLE tblPerson
ADD CONSTRAINT CK_tblPerson_Age
CHECK (Age > 0 AND Age < 150);
```

Now SQL Server validates the Age value whenever a record is inserted or updated.

### Important

The condition above allows ages from 1 to 149.

If the business requirement is to allow ages from 0 to 150, use:

```sql
CHECK (Age >= 0 AND Age <= 150);
```

---

## 3. General Syntax for Adding a CHECK Constraint

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name
CHECK (boolean_expression);
```

### Understanding the syntax

| Syntax | Explanation |
|---|---|
| ALTER TABLE | Modifies an existing table |
| table_name | Name of the table |
| ADD CONSTRAINT | Adds a new constraint |
| constraint_name | Name assigned to the constraint |
| CHECK | Defines the validation rule |
| boolean_expression | Condition that must be satisfied |

A CHECK constraint allows a row when its expression evaluates to TRUE or UNKNOWN. It rejects a row when the expression evaluates to FALSE.

---

## 4. Creating a CHECK Constraint — Practical Example

Let's create a person table.

### Step 1: Create the table

```sql
CREATE TABLE tblPerson
(
    PersonId INT PRIMARY KEY,
    PersonName VARCHAR(50),
    Age INT
);
```

### Step 2: Insert sample data

```sql
INSERT INTO tblPerson
VALUES
(1, 'Ravi', 25),
(2, 'Priya', 30),
(3, 'Arun', 45),
(4, 'Sneha', 18);
```

### Step 3: View the records

```sql
SELECT * FROM tblPerson;
```

### Step 4: Add a CHECK constraint

```sql
ALTER TABLE tblPerson
ADD CONSTRAINT CK_tblPerson_Age
CHECK (Age > 0 AND Age < 150);
```

### Step 5: Test valid data

```sql
INSERT INTO tblPerson
VALUES (5, 'Kiran', 35);
```

Expected behaviour: The record is inserted successfully.

### Step 6: Test invalid data

Try inserting a negative age:

```sql
INSERT INTO tblPerson
VALUES (6, 'John', -5);
```

Expected behaviour: SQL Server rejects the INSERT because the CHECK condition evaluates to FALSE.

Try inserting an age greater than 150:

```sql
INSERT INTO tblPerson
VALUES (7, 'David', 160);
```

Expected behaviour: SQL Server rejects the INSERT.

### Step 7: Test UPDATE

```sql
UPDATE tblPerson
SET Age = 200
WHERE PersonId = 1;
```

Expected behaviour: SQL Server rejects the UPDATE because the new Age value violates the CHECK constraint.

**Important:** CHECK constraints validate both INSERT and UPDATE operations.

---

## 5. Understanding NULL with CHECK Constraints

This is an important concept from the tutorial.

Consider this constraint:

```sql
CHECK (Age > 0 AND Age < 150);
```

What happens if we insert NULL into the Age column?

```sql
INSERT INTO tblPerson
VALUES (8, 'Manoj', NULL);
```

The record is allowed because the Age column is nullable and the CHECK expression evaluates to UNKNOWN.

### Understanding the three possible results

SQL Server conditions can produce three logical results:

| Result | Meaning | CHECK behaviour |
|---|---|---|
| TRUE | Condition is satisfied | Allows the row |
| FALSE | Condition is not satisfied | Rejects the row |
| UNKNOWN | Result cannot be determined, commonly because of NULL | Allows the row |

### Example

```sql
SELECT
    CASE
        WHEN 25 > 0 AND 25 < 150 THEN 'TRUE'
        ELSE 'NOT TRUE'
    END AS Result;
```

For NULL:

```sql
SELECT
    CASE
        WHEN NULL > 0 AND NULL < 150 THEN 'TRUE'
        WHEN NULL > 0 AND NULL < 150 IS NULL THEN 'UNKNOWN'
        ELSE 'FALSE'
    END AS Result;
```

### How to prevent NULL values?

If NULL should not be allowed, use NOT NULL along with the CHECK constraint.

```sql
CREATE TABLE tblPerson_NotNull
(
    PersonId INT PRIMARY KEY,
    PersonName VARCHAR(50),
    Age INT NOT NULL,
    CONSTRAINT CK_tblPerson_NotNull_Age
        CHECK (Age > 0 AND Age < 150)
);
```

Now:
- Age = 25 is accepted.
- Age = -5 is rejected.
- Age = 160 is rejected.
- Age = NULL is rejected because of NOT NULL.

**Remember:** CHECK and NOT NULL solve two different validation requirements.

---

## 6. CHECK Constraint with Multiple Conditions

A CHECK constraint can contain multiple conditions using logical operators such as AND and OR.

### Example 1 — Salary validation

An employee's salary should be greater than zero and less than 500000.

```sql
CHECK (Salary > 0 AND Salary < 500000);
```

### Example 2 — Product status validation

A product status should be either Active, Inactive, or Discontinued.

```sql
CHECK
(
    Status IN ('Active', 'Inactive', 'Discontinued')
);
```

### Example 3 — Discount validation

A discount percentage should be between 0 and 100.

```sql
CHECK (DiscountPercentage >= 0
       AND DiscountPercentage <= 100);
```

### Example 4 — Multiple columns

An employee's minimum salary should not be greater than their maximum salary.

```sql
CHECK (MinimumSalary <= MaximumSalary);
```

CHECK constraints can validate relationships between columns in the same row.

---

## 7. Adding a CHECK Constraint to an Existing Table

Sometimes a table already exists and we later decide to introduce a validation rule.

Let's practise with an employee table.

### Step 1: Create the table

```sql
CREATE TABLE tblEmployee
(
    EmployeeId INT PRIMARY KEY,
    EmployeeName VARCHAR(50),
    Salary DECIMAL(10,2)
);
```

### Step 2: Insert sample data

```sql
INSERT INTO tblEmployee
VALUES
(101, 'Ravi', 30000),
(102, 'Priya', 45000),
(103, 'Arun', 60000),
(104, 'Sneha', 35000);
```

### Step 3: Add the CHECK constraint

The salary should be greater than zero.

```sql
ALTER TABLE tblEmployee
ADD CONSTRAINT CK_tblEmployee_Salary
CHECK (Salary > 0);
```

### Step 4: Test the constraint

```sql
INSERT INTO tblEmployee
VALUES (105, 'Kiran', -10000);
```

Expected behaviour: SQL Server rejects the record.

### Important: Existing data

When adding a CHECK constraint to an existing table, SQL Server validates existing rows by default.

If existing records violate the new rule, adding the constraint can fail.

For this reason, review and correct existing data before adding a constraint.

---

## 8. Dropping a CHECK Constraint

Sometimes business requirements change and a validation rule is no longer required.

We can remove an existing CHECK constraint using ALTER TABLE.

### General syntax

```sql
ALTER TABLE table_name
DROP CONSTRAINT constraint_name;
```

### Example

```sql
ALTER TABLE tblPerson
DROP CONSTRAINT CK_tblPerson_Age;
```

After dropping the constraint, SQL Server no longer enforces that particular age validation rule.

Try inserting a record with an age of 200 after dropping the constraint and observe the result.

Remember: Dropping a CHECK constraint does not delete existing records or automatically change their values.

---

# 9. Hands-on Practice Scenarios

The following scenarios are designed to help students understand business requirements and independently implement SQL validation rules.

For each scenario:

1. Create the required table.
2. Insert the sample data.
3. Identify the validation requirement.
4. Create the CHECK constraint.
5. Test valid values.
6. Test invalid values.
7. Try updating existing records.
8. Explain the result.

## Scenario 1 — Person Age Validation

A system stores personal information.

The business requirement is:
- Age cannot be negative.
- Age must be less than 150.
- Age can be NULL.

Sample data:

| PersonId | PersonName | Age |
|---:|---|---:|
| 1 | Ravi | 25 |
| 2 | Priya | 30 |
| 3 | Arun | 45 |
| 4 | Sneha | 18 |

**Your tasks:**

1. Create a table named `tblPersonPractice`.
2. Insert the sample data.
3. Add a CHECK constraint for Age.
4. Insert a person with Age = 50.
5. Try inserting Age = -10.
6. Try inserting Age = 160.
7. Insert a person with Age = NULL.
8. Update an existing person's age to 200.
9. Observe which operations succeed and which fail.

## Scenario 2 — Employee Salary Validation

A company wants to ensure that employee salaries are always greater than zero.

Sample data:

| EmployeeId | EmployeeName | Salary |
|---:|---|---:|
| 101 | Ravi | 30000 |
| 102 | Priya | 45000 |
| 103 | Arun | 60000 |
| 104 | Sneha | 35000 |

**Your tasks:**

1. Create `tblEmployeePractice`.
2. Insert the sample data.
3. Add a CHECK constraint to ensure Salary is greater than zero.
4. Insert an employee with salary 55000.
5. Try inserting salary -5000.
6. Try inserting salary 0.
7. Update an employee's salary to -1000.
8. Observe the validation behaviour.

## Scenario 3 — Product Price Validation

An online store maintains product information.

Business requirements:
- Product price cannot be negative.
- Product price must be greater than zero.
- Product quantity cannot be negative.

Sample data:

| ProductId | ProductName | Price | Quantity |
|---:|---|---:|---:|
| 1 | Laptop | 55000 | 10 |
| 2 | Keyboard | 1500 | 25 |
| 3 | Mouse | 750 | 50 |
| 4 | Monitor | 12000 | 15 |

**Your tasks:**

1. Create `tblProductPractice`.
2. Insert the sample data.
3. Add a CHECK constraint for Price.
4. Add another CHECK constraint for Quantity.
5. Insert a product with a negative price.
6. Insert a product with a negative quantity.
7. Update an existing product with an invalid price.
8. Verify the results.

## Scenario 4 — Student Marks Validation

An educational institution stores student marks.

Business requirements:
- Marks cannot be less than zero.
- Marks cannot exceed 100.
- Student marks cannot be NULL.

Sample data:

| StudentId | StudentName | Marks |
|---:|---|---:|
| 1 | Rahul | 85 |
| 2 | Anjali | 92 |
| 3 | Kiran | 76 |
| 4 | Meena | 68 |

**Your tasks:**

1. Create `tblStudentPractice`.
2. Define Marks as NOT NULL.
3. Add a CHECK constraint for the valid marks range.
4. Insert a student with marks 95.
5. Try inserting marks -5.
6. Try inserting marks 110.
7. Try inserting NULL.
8. Update an existing student's marks to 105.

## Scenario 5 — Customer Rating Validation

A website allows customers to rate a product.

Business requirements:
- Rating must be between 1 and 5.
- Only integer ratings are allowed.
- Rating cannot be NULL.

Sample data:

| ReviewId | CustomerName | Rating |
|---:|---|---:|
| 1 | Ravi | 5 |
| 2 | Priya | 4 |
| 3 | Arun | 3 |
| 4 | Sneha | 5 |

**Your tasks:**

1. Create `tblCustomerRating`.
2. Insert the sample data.
3. Add a CHECK constraint for Rating.
4. Insert ratings 1 and 5.
5. Try inserting rating 0.
6. Try inserting rating 6.
7. Try inserting NULL.
8. Update a rating to 10.

## Scenario 6 — Order Quantity and Discount

An order management system stores quantity and discount percentage.

Business requirements:
- Order quantity must be greater than zero.
- Discount must be between 0 and 100.
- Quantity and discount cannot be NULL.

Sample data:

| OrderId | Quantity | DiscountPercentage |
|---:|---:|---:|
| 101 | 2 | 10 |
| 102 | 5 | 15 |
| 103 | 1 | 0 |
| 104 | 10 | 20 |

**Your tasks:**

1. Create `tblOrderPractice`.
2. Insert the sample data.
3. Add a CHECK constraint for Quantity.
4. Add a CHECK constraint for DiscountPercentage.
5. Insert an order with quantity 0.
6. Insert an order with discount 120.
7. Try inserting a negative discount.
8. Update an existing order with invalid quantity.

---

# 10. Additional Challenge — Multiple Column CHECK Constraint

A company maintains employee salary information.

Business requirement:

- Minimum salary must be greater than zero.
- Maximum salary must be greater than or equal to minimum salary.

Sample data:

| EmployeeId | EmployeeName | MinimumSalary | MaximumSalary |
|---:|---|---:|---:|
| 101 | Ravi | 20000 | 40000 |
| 102 | Priya | 30000 | 60000 |
| 103 | Arun | 25000 | 50000 |

**Your tasks:**

1. Create a table named `tblSalaryRange`.
2. Insert the sample data.
3. Create a CHECK constraint to validate MinimumSalary.
4. Create another CHECK constraint to ensure MaximumSalary is greater than or equal to MinimumSalary.
5. Insert a record where MaximumSalary is less than MinimumSalary.
6. Update an existing record with an invalid salary range.
7. Observe the result.

---

# 11. Revision Questions

1. What is a CHECK constraint?
2. Why do we use CHECK constraints?
3. What is the general syntax for adding a CHECK constraint?
4. What happens when a CHECK expression evaluates to FALSE?
5. Can a CHECK constraint validate data during UPDATE?
6. What happens when a CHECK expression evaluates to UNKNOWN?
7. Why can a nullable column accept NULL even when a CHECK constraint exists?
8. How can we prevent NULL values?
9. Can a CHECK constraint validate multiple columns?
10. How do we remove an existing CHECK constraint?
11. What happens if existing data violates a CHECK constraint when we try to add it?
12. What is the difference between CHECK and NOT NULL?

---

# 12. Completion Checklist

- [ ] I understand what a CHECK constraint is.
- [ ] I understand why validation is important.
- [ ] I created a CHECK constraint using ALTER TABLE.
- [ ] I tested valid INSERT statements.
- [ ] I tested invalid INSERT statements.
- [ ] I tested invalid UPDATE statements.
- [ ] I understand TRUE, FALSE and UNKNOWN.
- [ ] I understand NULL behaviour.
- [ ] I created CHECK constraints with multiple conditions.
- [ ] I created a CHECK constraint involving multiple columns.
- [ ] I dropped an existing CHECK constraint.
- [ ] I completed the practice scenarios.

---

# 13. SQL Server Documentation

- [CHECK constraints — SQL Server](https://learn.microsoft.com/en-us/sql/relational-databases/tables/create-check-constraints)
- [ALTER TABLE — Transact-SQL](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql)
- [CREATE TABLE — Transact-SQL](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-table-transact-sql)

## Instructor Notes

- Students should execute all examples in SQL Server Management Studio (SSMS).
- Use separate practice tables for independent scenarios.
- Ask students to predict the outcome before executing INSERT and UPDATE statements.
- Encourage students to read and understand SQL Server error messages.
- Students should write their own CHECK expressions instead of copying every example.
- Emphasize the difference between CHECK constraints and NOT NULL constraints.
- Explain that CHECK constraints validate each row; they are not intended to validate data against other rows or tables.
