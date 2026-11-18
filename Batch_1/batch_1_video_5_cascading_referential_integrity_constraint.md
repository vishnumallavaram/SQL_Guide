# Batch 1 — Video 5: Cascading Referential Integrity Constraint

## 1. Learning Objectives

By the end of this practice, you should be able to:

- Understand cascading referential integrity.
- Understand the relationship between parent and child tables.
- Explain what happens when a referenced parent record is deleted or updated.
- Understand the four referential actions:
 - NO ACTION
 - CASCADE
 - SET NULL
 - SET DEFAULT
- Create foreign-key constraints using SQL Server.
- Test DELETE and UPDATE operations with different referential actions.

## 2. What is Cascading Referential Integrity?

Cascading referential integrity defines what SQL Server should do when a user tries to delete or update a key in a parent table that is referenced by a foreign key in another table.

Consider two tables:

**Parent table: tblGender**

| ID | Gender |
|---:|---|
| 1 | Male |
| 2 | Female |
| 3 | Not Specified |

**Child table: tblPerson**

| ID | Name | GenderId |
|---:|---|---:|
| 101 | Ravi | 1 |
| 102 | Priya | 2 |
| 103 | Arun | 1 |
| 104 | Sneha | 2 |
| 105 | Kiran | 3 |

Here:

- `tblGender` is the parent table.
- `tblPerson` is the child table.
- `tblGender.ID` is the referenced key.
- `tblPerson.GenderId` is the foreign key.

For example, Ravi and Arun have `GenderId = 1`.

If we delete Gender ID 1, what should happen to Ravi and Arun?

SQL Server provides four referential actions to control this behaviour.

---

## 3. Understanding the Four Referential Actions

### 3.1 NO ACTION

NO ACTION is the default behaviour.

If a parent record is referenced by existing child records, SQL Server prevents the DELETE or UPDATE operation and raises an error.

**Example:**

If Gender ID 1 is referenced by Ravi and Arun, SQL Server will not allow us to delete Gender ID 1.

```sql
DELETE FROM tblGender
WHERE ID = 1;
```

**Expected behaviour:**

- SQL Server raises a foreign-key error.
- The DELETE operation is rolled back.
- Ravi and Arun remain in the child table.
- The parent record remains unchanged.

**Real-world example:**

A company cannot delete a department while employees are still assigned to that department.

### 3.2 CASCADE

CASCADE automatically applies the parent DELETE or UPDATE operation to the related child records.

**Example — ON DELETE CASCADE:**

If Gender ID 1 is deleted, all people referencing Gender ID 1 are also deleted.

**Expected behaviour:**

- Gender ID 1 is deleted.
- Ravi and Arun are also deleted.
- Other gender and person records remain unchanged.

**Example — ON UPDATE CASCADE:**

If Gender ID 1 is changed to 10, all child records referencing ID 1 are automatically updated to 10.

**Real-world example:**

When a parent category is removed, the associated child records may also need to be removed.

### 3.3 SET NULL

SET NULL changes the referencing foreign-key values in the child table to NULL when the referenced parent key is deleted or updated.

**Example — ON DELETE SET NULL:**

If Gender ID 1 is deleted:

- The parent record is deleted.
- Ravi's `GenderId` becomes NULL.
- Arun's `GenderId` becomes NULL.
- Other records remain unchanged.

**Important:** The foreign-key column must allow NULL values.

**Real-world example:**

If a manager leaves the company, employees previously assigned to that manager may temporarily have no manager assigned.

### 3.4 SET DEFAULT

SET DEFAULT assigns the default value defined on the child foreign-key column when the referenced parent key is deleted or updated.

**Example — ON DELETE SET DEFAULT:**

Suppose `GenderId` has a default value of 3.

If Gender ID 1 is deleted:

- The parent record is deleted.
- Ravi's `GenderId` becomes 3.
- Arun's `GenderId` becomes 3.

**Important:**

- A DEFAULT constraint must exist on the child foreign-key column.
- The default value must refer to a valid parent key for the operation to succeed.
- If the default value does not satisfy the foreign-key relationship, the operation can fail.

**Real-world example:**

If a product category is removed, associated products may be assigned to a default category such as "Uncategorized".

---

## 4. SQL Server Syntax

### Creating a foreign key with NO ACTION

```sql
FOREIGN KEY (GenderId)
REFERENCES tblGender(ID)
ON DELETE NO ACTION
ON UPDATE NO ACTION;
```

### Creating a foreign key with CASCADE

```sql
FOREIGN KEY (GenderId)
REFERENCES tblGender(ID)
ON DELETE CASCADE
ON UPDATE CASCADE;
```

### Creating a foreign key with SET NULL

```sql
FOREIGN KEY (GenderId)
REFERENCES tblGender(ID)
ON DELETE SET NULL
ON UPDATE SET NULL;
```

### Creating a foreign key with SET DEFAULT

```sql
FOREIGN KEY (GenderId)
REFERENCES tblGender(ID)
ON DELETE SET DEFAULT
ON UPDATE SET DEFAULT;
```

**Note:** SQL Server allows different actions for DELETE and UPDATE. The examples above use the same action for both to make learning easier.

---

# 5. Hands-on Practice — Sample Data

We will use the same parent and child data for all four scenarios.

Each scenario uses separate tables so that one exercise does not affect another.

Run each scenario independently in SQL Server Management Studio (SSMS).

## Scenario 1 — NO ACTION

### Step 1: Create parent table

```sql
CREATE TABLE tblGender_NoAction
(
 ID INT PRIMARY KEY,
 Gender VARCHAR(20)
);
```

### Step 2: Create child table

```sql
CREATE TABLE tblPerson_NoAction
(
 ID INT PRIMARY KEY,
 Name VARCHAR(50),
 GenderId INT,
 CONSTRAINT FK_Person_Gender_NoAction
 FOREIGN KEY (GenderId)
 REFERENCES tblGender_NoAction(ID)
 ON DELETE NO ACTION
 ON UPDATE NO ACTION
);
```

### Step 3: Insert parent data

```sql
INSERT INTO tblGender_NoAction (ID, Gender)
VALUES
(1, 'Male'),
(2, 'Female'),
(3, 'Not Specified');
```

### Step 4: Insert child data

```sql
INSERT INTO tblPerson_NoAction (ID, Name, GenderId)
VALUES
(101, 'Ravi', 1),
(102, 'Priya', 2),
(103, 'Arun', 1),
(104, 'Sneha', 2),
(105, 'Kiran', 3);
```

### Step 5: Verify the data

```sql
SELECT * FROM tblGender_NoAction;
SELECT * FROM tblPerson_NoAction;
```

### Step 6: Try deleting Gender ID 1

```sql
DELETE FROM tblGender_NoAction
WHERE ID = 1;
```

**Expected result:**

SQL Server raises a foreign-key error because child records reference Gender ID 1.

### Step 7: Try updating Gender ID 1

```sql
UPDATE tblGender_NoAction
SET ID = 10
WHERE ID = 1;
```

**Expected result:**

SQL Server raises a foreign-key error. The parent key cannot be updated while child records reference it.

**Your observation:**

Check whether the parent and child records remain unchanged.

---

## Scenario 2 — CASCADE

### Step 1: Create parent table

```sql
CREATE TABLE tblGender_Cascade
(
 ID INT PRIMARY KEY,
 Gender VARCHAR(20)
);
```

### Step 2: Create child table

```sql
CREATE TABLE tblPerson_Cascade
(
 ID INT PRIMARY KEY,
 Name VARCHAR(50),
 GenderId INT,
 CONSTRAINT FK_Person_Gender_Cascade
 FOREIGN KEY (GenderId)
 REFERENCES tblGender_Cascade(ID)
 ON DELETE CASCADE
 ON UPDATE CASCADE
);
```

### Step 3: Insert sample data

```sql
INSERT INTO tblGender_Cascade (ID, Gender)
VALUES
(1, 'Male'),
(2, 'Female'),
(3, 'Not Specified');

INSERT INTO tblPerson_Cascade (ID, Name, GenderId)
VALUES
(101, 'Ravi', 1),
(102, 'Priya', 2),
(103, 'Arun', 1),
(104, 'Sneha', 2),
(105, 'Kiran', 3);
```

### Step 4: Test ON DELETE CASCADE

```sql
DELETE FROM tblGender_Cascade
WHERE ID = 1;
```

Verify:

```sql
SELECT * FROM tblGender_Cascade;
SELECT * FROM tblPerson_Cascade;
```

**Expected result:**

- Gender ID 1 is deleted.
- Ravi and Arun are automatically deleted from the child table.
- Priya, Sneha, and Kiran remain.

### Step 5: Test ON UPDATE CASCADE

For this test, recreate the tables and insert the original sample data again, or use a fresh copy of the tables.

```sql
UPDATE tblGender_Cascade
SET ID = 10
WHERE ID = 1;
```

Verify:

```sql
SELECT * FROM tblGender_Cascade;
SELECT * FROM tblPerson_Cascade;
```

**Expected result:**

- Parent Gender ID 1 becomes 10.
- Ravi's GenderId becomes 10.
- Arun's GenderId becomes 10.
- Other records remain unchanged.

---

## Scenario 3 — SET NULL

### Step 1: Create parent table

```sql
CREATE TABLE tblGender_SetNull
(
 ID INT PRIMARY KEY,
 Gender VARCHAR(20)
);
```

### Step 2: Create child table

```sql
CREATE TABLE tblPerson_SetNull
(
 ID INT PRIMARY KEY,
 Name VARCHAR(50),
 GenderId INT NULL,
 CONSTRAINT FK_Person_Gender_SetNull
 FOREIGN KEY (GenderId)
 REFERENCES tblGender_SetNull(ID)
 ON DELETE SET NULL
 ON UPDATE SET NULL
);
```

### Step 3: Insert sample data

```sql
INSERT INTO tblGender_SetNull (ID, Gender)
VALUES
(1, 'Male'),
(2, 'Female'),
(3, 'Not Specified');

INSERT INTO tblPerson_SetNull (ID, Name, GenderId)
VALUES
(101, 'Ravi', 1),
(102, 'Priya', 2),
(103, 'Arun', 1),
(104, 'Sneha', 2),
(105, 'Kiran', 3);
```

### Step 4: Test ON DELETE SET NULL

```sql
DELETE FROM tblGender_SetNull
WHERE ID = 1;
```

Verify:

```sql
SELECT * FROM tblGender_SetNull;
SELECT * FROM tblPerson_SetNull;
```

**Expected result:**

| ID | Name | GenderId |
|---:|---|---:|
| 101 | Ravi | NULL |
| 102 | Priya | 2 |
| 103 | Arun | NULL |
| 104 | Sneha | 2 |
| 105 | Kiran | 3 |

### Step 5: Test ON UPDATE SET NULL

Recreate the tables and insert the original sample data before running this test.

```sql
UPDATE tblGender_SetNull
SET ID = 10
WHERE ID = 1;
```

**Expected result:**

- Parent Gender ID 1 becomes 10.
- Ravi's GenderId becomes NULL.
- Arun's GenderId becomes NULL.
- Other child records remain unchanged.

---

## Scenario 4 — SET DEFAULT

### Step 1: Create parent table

```sql
CREATE TABLE tblGender_SetDefault
(
 ID INT PRIMARY KEY,
 Gender VARCHAR(20)
);
```

### Step 2: Create child table

Notice the DEFAULT constraint on `GenderId`.

```sql
CREATE TABLE tblPerson_SetDefault
(
 ID INT PRIMARY KEY,
 Name VARCHAR(50),
 GenderId INT NOT NULL
 CONSTRAINT DF_Person_GenderId DEFAULT 3,

 CONSTRAINT FK_Person_Gender_SetDefault
 FOREIGN KEY (GenderId)
 REFERENCES tblGender_SetDefault(ID)
 ON DELETE SET DEFAULT
 ON UPDATE SET DEFAULT
);
```

### Step 3: Insert sample data

```sql
INSERT INTO tblGender_SetDefault (ID, Gender)
VALUES
(1, 'Male'),
(2, 'Female'),
(3, 'Not Specified');

INSERT INTO tblPerson_SetDefault (ID, Name, GenderId)
VALUES
(101, 'Ravi', 1),
(102, 'Priya', 2),
(103, 'Arun', 1),
(104, 'Sneha', 2),
(105, 'Kiran', 3);
```

### Step 4: Test ON DELETE SET DEFAULT

```sql
DELETE FROM tblGender_SetDefault
WHERE ID = 1;
```

Verify:

```sql
SELECT * FROM tblGender_SetDefault;
SELECT * FROM tblPerson_SetDefault;
```

**Expected result:**

| ID | Name | GenderId |
|---:|---|---:|
| 101 | Ravi | 3 |
| 102 | Priya | 2 |
| 103 | Arun | 3 |
| 104 | Sneha | 2 |
| 105 | Kiran | 3 |

Ravi and Arun are assigned the default GenderId of 3.

### Step 5: Test ON UPDATE SET DEFAULT

Recreate the tables and insert the original sample data before running this test.

```sql
UPDATE tblGender_SetDefault
SET ID = 10
WHERE ID = 1;
```

**Expected result:**

- Parent Gender ID 1 becomes 10.
- Ravi's GenderId becomes 3.
- Arun's GenderId becomes 3.
- Other child records remain unchanged.

---

# 6. Additional Real-World Practice Scenarios

Complete these exercises independently after understanding the four actions.

## Practice 1 — Department and Employee

A company maintains two tables:

**Department**

| DepartmentId | DepartmentName |
|---:|---|
| 1 | IT |
| 2 | HR |
| 3 | Finance |

**Employee**

| EmployeeId | EmployeeName | DepartmentId |
|---:|---|---:|
| 101 | Ravi | 1 |
| 102 | Priya | 2 |
| 103 | Arun | 1 |
| 104 | Sneha | 3 |

Tasks:

1. Create both tables.
2. Establish a foreign-key relationship.
3. Configure NO ACTION.
4. Try deleting the IT department.
5. Change the relationship to CASCADE and test again.
6. Observe what happens to employees assigned to IT.

## Practice 2 — Customer and Orders

A retail company maintains customer information and their orders.

**Customers**

| CustomerId | CustomerName |
|---:|---|
| 1 | Ravi |
| 2 | Priya |
| 3 | Arun |

**Orders**

| OrderId | CustomerId | OrderAmount |
|---:|---:|---:|
| 101 | 1 | 5000 |
| 102 | 2 | 3000 |
| 103 | 1 | 2500 |
| 104 | 3 | 4500 |

Tasks:

1. Create the parent and child tables.
2. Create a foreign-key constraint using NO ACTION.
3. Try deleting customer ID 1.
4. Observe the error.
5. Create another version using CASCADE.
6. Delete customer ID 1 and observe the related orders.

**Think about it:** In a real business, should customer deletion automatically delete order history? Explain your choice.

## Practice 3 — Product and Category

A company maintains product categories.

**Category**

| CategoryId | CategoryName |
|---:|---|
| 1 | Electronics |
| 2 | Furniture |
| 3 | Uncategorized |

**Product**

| ProductId | ProductName | CategoryId |
|---:|---|---:|
| 101 | Laptop | 1 |
| 102 | Chair | 2 |
| 103 | Mobile | 1 |
| 104 | Table | 2 |

Tasks:

1. Create both tables.
2. Set the default category to 3.
3. Configure the foreign key with SET DEFAULT.
4. Delete category ID 1.
5. Check which category is assigned to Laptop and Mobile.
6. Try updating a category key and observe the result.

---

# 7. Comparison of Referential Actions

| Action | Parent DELETE/UPDATE | Child records |
|---|---|---|
| NO ACTION | Prevented when referenced | Unchanged |
| CASCADE | Allowed | Automatically deleted/updated |
| SET NULL | Allowed | Foreign-key value becomes NULL |
| SET DEFAULT | Allowed | Foreign-key value becomes default |

## 8. Important Points to Remember

1. A foreign key establishes a relationship between parent and child tables.
2. NO ACTION is the default referential behaviour in SQL Server.
3. CASCADE automatically propagates the parent DELETE or UPDATE operation.
4. SET NULL requires the foreign-key column to allow NULL.
5. SET DEFAULT requires a DEFAULT constraint on the foreign-key column.
6. For SET DEFAULT, the resulting default value must satisfy the foreign-key relationship.
7. DELETE and UPDATE actions can be configured separately.
8. Cascading actions can affect multiple child records.

**Important SQL Server note:** SQL Server may reject cascading foreign-key definitions that create multiple cascade paths or cycles. Design the relationships carefully.

---

# 9. Revision Questions

1. What is cascading referential integrity?
2. What is the relationship between a parent and child table?
3. What happens when we delete a referenced parent record using NO ACTION?
4. What is the difference between ON DELETE CASCADE and ON UPDATE CASCADE?
5. What does SET NULL do?
6. What condition must be satisfied before using SET NULL?
7. What is the purpose of SET DEFAULT?
8. Why do we need a DEFAULT constraint when using SET DEFAULT?
9. Can DELETE and UPDATE have different referential actions?
10. What happens if the default value does not reference a valid parent record?

# 10. Completion Checklist

- [ ] I understand parent and child table relationships.
- [ ] I understand NO ACTION.
- [ ] I understand CASCADE.
- [ ] I understand SET NULL.
- [ ] I understand SET DEFAULT.
- [ ] I created parent and child tables in SSMS.
- [ ] I inserted the sample data.
- [ ] I tested DELETE operations.
- [ ] I tested UPDATE operations.
- [ ] I completed the real-world practice scenarios.

## 11. Microsoft SQL Server Documentation

- [FOREIGN KEY and REFERENCES constraints](https://learn.microsoft.com/en-us/sql/relational-databases/tables/create-foreign-key-relationships)
- [CREATE TABLE — Transact-SQL](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-table-transact-sql)
- [ALTER TABLE — Transact-SQL](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-table-transact-sql)

**Instructor note:** Students should run each scenario using its own tables. For the CASCADE, SET NULL, and SET DEFAULT update tests, restore the original sample data before running the update test. This avoids confusion caused by earlier DELETE operations.
