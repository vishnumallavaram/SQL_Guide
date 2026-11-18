
# Batch 1 — Video 4: DEFAULT Values and ALTER TABLE

## 1. Learning objectives

By the end of this practice, you should be able to:

- Explain what a `DEFAULT` value does.
- Create a table with a default value.
- Insert rows with and without supplying a value for a column.
- Understand the difference between an omitted value and an explicitly supplied `NULL`.
- Add a default value to a column in an existing table.
- Remove a default value from a column.

## 2. What is a DEFAULT value?

A default value is a value the database supplies automatically when an `INSERT` statement does not provide a value for that column.

Imagine an employee table with an `employment_status` column. Most new employees start with the status `Active`. Instead of typing `Active` every time, we can define it as the default.

```sql
CREATE TABLE employees_default_demo (
    employee_id INT,
    employee_name STRING,
    employment_status STRING DEFAULT 'Active'
);
```

The default is defined next to the column name.

> Databricks note: Column defaults are supported in Databricks SQL and Databricks Runtime 11.3 LTS and above. Your workspace must support the feature and you must have the required permissions.

## 3. Insert a row without specifying the default column

```sql
INSERT INTO employees_default_demo (employee_id, employee_name)
VALUES (101, 'Anita');

SELECT * FROM employees_default_demo;
```

Expected result:

| employee_id | employee_name | employment_status |
|---:|---|---|
| 101 | Anita | Active |

Because `employment_status` was omitted from the INSERT column list, Databricks supplied the default value.

## 4. Insert a row with an explicit value

```sql
INSERT INTO employees_default_demo
    (employee_id, employee_name, employment_status)
VALUES
    (102, 'Rahul', 'On Leave');

SELECT * FROM employees_default_demo;
```

Expected result includes:

| employee_id | employee_name | employment_status |
|---:|---|---|
| 102 | Rahul | On Leave |

The value supplied in the INSERT statement is used instead of the default.

## 5. Omitted value versus NULL

These two inserts are different.

**Omitting the column:**

```sql
INSERT INTO employees_default_demo (employee_id, employee_name)
VALUES (103, 'Meera');
```

The default value `Active` is applied.

**Explicitly supplying NULL:**

```sql
INSERT INTO employees_default_demo
    (employee_id, employee_name, employment_status)
VALUES
    (104, 'Kiran', NULL);
```

The supplied value is `NULL`; the default is not substituted.

Remember:
- Column omitted from INSERT: the default is used, if one is defined.
- Column explicitly assigned `NULL`: `NULL` is inserted, if the column allows it.
- A supplied value: that supplied value is inserted.

## 6. Add a DEFAULT to an existing table using ALTER TABLE

Sometimes a table already exists and we later decide that a column should have a default value.

First, create a table without a default:

```sql
CREATE TABLE store_default_demo (
    store_id INT,
    store_name STRING,
    store_status STRING
);
```

Now add a default to the existing `store_status` column:

```sql
ALTER TABLE store_default_demo
ALTER COLUMN store_status SET DEFAULT 'Open';
```

Insert a row without specifying `store_status`:

```sql
INSERT INTO store_default_demo (store_id, store_name)
VALUES (201, 'Hyderabad Central');

SELECT * FROM store_default_demo;
```

Expected result:

| store_id | store_name | store_status |
|---:|---|---|
| 201 | Hyderabad Central | Open |

The default affects new inserts where the column is omitted. Setting a default on an existing column does not update the values already stored in existing rows.

## 7. Remove a DEFAULT using ALTER TABLE

If the business no longer wants the default value, remove it from the column:

```sql
ALTER TABLE store_default_demo
ALTER COLUMN store_status DROP DEFAULT;
```

Try inserting another row without `store_status`:

```sql
INSERT INTO store_default_demo (store_id, store_name)
VALUES (202, 'Chennai Central');

SELECT * FROM store_default_demo
ORDER BY store_id;
```

For a nullable column, after the default is removed, an omitted value results in `NULL`.

---

## 8. SQL Server tutorial syntax versus Databricks SQL

The tutorial demonstrates SQL Server syntax:

```sql
ALTER TABLE tblPerson
ADD CONSTRAINT DF_tblPerson_GenderId
DEFAULT 1 FOR GenderId;
```

To remove that named SQL Server constraint:

```sql
ALTER TABLE tblPerson
DROP CONSTRAINT DF_tblPerson_GenderId;
```

Databricks uses a column-level default expression rather than this named-default-constraint syntax.

**Set a default in Databricks SQL:**

```sql
ALTER TABLE table_name
ALTER COLUMN column_name SET DEFAULT default_value;
```

**Remove a default in Databricks SQL:**

```sql
ALTER TABLE table_name
ALTER COLUMN column_name DROP DEFAULT;
```

For this lesson, understand the concept from the SQL Server video, then practise the Databricks syntax above. Do not copy the SQL Server `ADD CONSTRAINT ... DEFAULT ... FOR` or named `DROP CONSTRAINT` statements into Databricks SQL for a column default.

---

# 9. Hands-on practice scenarios

Complete the scenarios in Databricks SQL Editor. Create the practice tables in your approved training schema. Try each task yourself before checking the expected behaviour.

## Scenario 1 — New employee onboarding

A company wants every new employee to have the employment status `Active` unless another status is provided.

**Your tasks**

1. Create a table named `employee_onboarding_practice`.
2. Add these columns: `employee_id`, `employee_name`, and `employment_status`.
3. Define `Active` as the default for `employment_status`.
4. Insert an employee without specifying `employment_status`.
5. Insert another employee and explicitly set the status to `On Leave`.
6. Query the table and compare both records.

**Think about it:** Why did the two employees receive different employment statuses?

## Scenario 2 — New store registration

A retail company wants every newly registered store to have the status `Pending` by default.

**Your tasks**

1. Create a table named `store_registration_practice` with `store_id`, `store_name`, and `approval_status`.
2. Insert one store without providing `approval_status`.
3. Insert another store with `approval_status` set to `Approved`.
4. Query the table and verify both values.
5. Add a default value of `Pending` to `approval_status` using `ALTER TABLE` instead of defining it during table creation.
6. Insert one more store without specifying `approval_status` and check the result.

**Think about it:** Does adding a default to an existing column change the values in rows that were already inserted?

## Scenario 3 — Product availability

A product table should use `Available` as the default availability status for new products.

**Your tasks**

1. Create a table named `product_availability_practice` with `product_id`, `product_name`, and `availability_status`.
2. Define `Available` as the default for `availability_status`.
3. Insert a product without specifying the availability status.
4. Insert a second product with the status `Discontinued`.
5. Insert a third product with an explicit `NULL`, if the column allows it.
6. Query all rows and compare the three results.

**Think about it:** Why does an explicit `NULL` not receive the default value?

## Scenario 4 — Business changes the default

The store team initially used `Open` as the default store status. The business now wants new stores to start with `Under Review`.

**Your tasks**

1. Create a practice table with a `store_status` column and a default value of `Open`.
2. Insert a row without specifying `store_status`.
3. Change the column default to `Under Review` using `ALTER TABLE`.
4. Insert another row without specifying `store_status`.
5. Query the table and compare the two rows.
6. Explain why the first row still has its original value.

## Scenario 5 — Remove a default

The business decides that store status must be entered explicitly from now on.

**Your tasks**

1. Use the table from Scenario 4.
2. Remove the default from `store_status`.
3. Insert a new row without specifying `store_status`.
4. Query the table.
5. Explain what happened after the default was removed.

---

## 10. Revision questions

1. What is a DEFAULT value?
2. When does the database apply a default value during INSERT?
3. What is the difference between omitting a column and inserting `NULL`?
4. Which command adds a default to an existing column in Databricks SQL?
5. Which command removes a default from an existing column in Databricks SQL?
6. Does changing a column's default update old records?

## 11. Completion checklist

- [ ] I understand why DEFAULT values are used.
- [ ] I created a table with a default value.
- [ ] I inserted a row without specifying the default column.
- [ ] I inserted a row with my own value.
- [ ] I tested an explicit `NULL`.
- [ ] I added a default to an existing column.
- [ ] I changed and removed a default using `ALTER TABLE`.
- [ ] I completed the practice scenarios.

## 12. Databricks documentation

- [ALTER TABLE — Databricks SQL](https://learn.microsoft.com/en-us/azure/databricks/sql/language-manual/sql-ref-syntax-ddl-alter-table)
- [ALTER TABLE column clause — Databricks SQL](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-ddl-alter-table-manage-column)

**Instructor note:** Default values in Databricks SQL are supported in Databricks SQL and Databricks Runtime 11.3 LTS and above. Students should use an instructor-approved training schema and verify workspace support and permissions before running DDL statements.
