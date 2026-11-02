# Batch 1 --- Video 2: Accessing Databricks SQL Editor and Exploring the Workspace

## Learning objectives

By completing this exercise, students will practise:

-   Accessing the Databricks workspace.
-   Opening Databricks SQL Editor.
-   Understanding the purpose of Catalog Explorer.
-   Identifying catalogs, schemas, and tables.
-   Selecting the catalog and schema to work with.
-   Executing and validating a simple SQL query.

## Learning approach

The assigned tutorial demonstrates connecting to SQL Server using SQL
Server Management Studio (SSMS).

Watch the video to understand the concepts of connecting to a database
environment, identifying the server or instance, choosing an
authentication method, selecting a database, and opening a query window.

**Practical work for this course must be completed in Databricks SQL
Editor.** Students do not need to install or use SSMS for this exercise.

## Environment

## Databricks links

- [Open Databricks](https://www.databricks.com/) — use your course or organization workspace URL to sign in.
- [SQL Editor overview](https://learn.microsoft.com/en-us/azure/databricks/sql/user/sql-editor/) — learn how to open and use the SQL Editor.
- [Write queries in SQL Editor](https://learn.microsoft.com/en-us/azure/databricks/sql/user/sql-editor/write-queries) — learn how to connect to compute, browse data objects, and write queries.

> Your actual workspace address is specific to your account or organization. Use the workspace URL supplied by your instructor.


Use **Databricks workspace** and **Databricks SQL Editor**.

> **Platform mapping:** SSMS is the tool shown in the tutorial.
> Databricks SQL Editor is the practical environment used in this
> course. A SQL Server instance is not the same thing as a Databricks
> workspace. In Databricks, students will work with the workspace, SQL
> Editor, catalogs, schemas, and tables.

## Student instructions

1.  Sign in to the Databricks workspace provided for the course.
2.  Locate and open SQL Editor.
3.  Open Catalog Explorer.
4.  Identify the catalogs available to your user.
5.  Expand a catalog and identify its schemas.
6.  Expand a schema and identify its tables, if any are available.
7.  Open a SQL query and execute the practice statements below.
8.  Record the catalog and schema that are active in your session.

## Part A --- Explore the Databricks workspace

Complete the following tasks using the Databricks user interface:

1.  Sign in to the workspace.
2.  Open SQL Editor.
3.  Locate Catalog Explorer.
4.  Identify at least one catalog that you are allowed to access.
5.  Expand the catalog and inspect its schemas.
6.  If a schema contains tables, open the table details and identify its
    columns.

### Questions

-   What is the name of the Databricks workspace you accessed?
-   Which catalog did you inspect?
-   Which schemas are visible under that catalog?
-   What is the difference between a catalog and a schema?
-   Where do you open and execute SQL statements?

## Part B --- Execute a first query

Run the following query in Databricks SQL Editor:

``` sql
SELECT 1 AS test_value;
```

### Expected result

The query should return one row with a column named `test_value` and a
value of `1`.

Now run:

``` sql
SELECT current_catalog() AS current_catalog,
       current_schema() AS current_schema;
```

Record the catalog and schema returned by the query.

> The current catalog and schema depend on your workspace and session
> context. Do not assume that every student will see the same names.

## Part C --- Explore catalogs and schemas using SQL

Run the following statements, one at a time:

``` sql
SHOW CATALOGS;
```

``` sql
SHOW SCHEMAS IN <catalog_name>;
```

Replace `<catalog_name>` with a catalog that you are permitted to
inspect. Do not type the angle brackets.

If your workspace permissions allow it, select a catalog and schema:

``` sql
USE CATALOG <catalog_name>;
```

``` sql
USE SCHEMA <schema_name>;
```

Then verify the active context:

``` sql
SELECT current_catalog() AS current_catalog,
       current_schema() AS current_schema;
```

## Part D --- Practice tasks

### Task 1: Workspace navigation

Open Databricks SQL Editor and Catalog Explorer. Identify one catalog
and at least one schema visible to you.

### Task 2: Run a basic query

Execute `SELECT 1 AS test_value;` and verify the output.

### Task 3: Identify the active context

Use `current_catalog()` and `current_schema()` to identify the active
catalog and schema.

### Task 4: List available catalogs

Use `SHOW CATALOGS;` and record the catalogs visible to your user.

### Task 5: List schemas

Choose a catalog you can access and list its schemas using
`SHOW SCHEMAS IN`.

### Task 6: Change the working context

If your permissions allow it, use `USE CATALOG` and `USE SCHEMA` to
change the active context. Verify the result.

## Part E --- Validation checklist

Confirm that you can:

-   [ ] Sign in to the Databricks workspace.
-   [ ] Open SQL Editor.
-   [ ] Locate Catalog Explorer.
-   [ ] Identify a catalog and its schemas.
-   [ ] Execute a simple SQL query.
-   [ ] Display the current catalog and schema.
-   [ ] List catalogs and schemas that your user can access.
-   [ ] Select a catalog and schema, if permitted.

## Common issues to understand

-   **Workspace access:** You may not be able to sign in if your account
    has not been provisioned or the workspace URL is incorrect.
-   **Permission errors:** A catalog or schema may be visible but not
    accessible for every operation. Access depends on the permissions
    granted to your user.
-   **No tables displayed:** A schema may be empty, or you may not have
    permission to view its objects.
-   **Wrong context:** A query that uses an unqualified table name may
    look in the current catalog and schema. Check the active context
    before querying tables.
-   **SQL warehouse or compute availability:** Query execution may
    require an available SQL warehouse or supported compute resource,
    depending on the workspace setup.

## Key learning points

-   SSMS is the SQL Server client shown in the tutorial; Databricks SQL
    Editor is the tool used for practical work in this course.
-   A Databricks workspace provides access to Databricks services and
    objects.
-   The Unity Catalog hierarchy used in this course is **catalog →
    schema → table**.
-   `current_catalog()` and `current_schema()` help identify the active
    context.
-   `SHOW CATALOGS` and `SHOW SCHEMAS` help explore available objects.
-   Permissions determine which catalogs, schemas, and tables you can
    access.


------------------------------------------------------------------------

# Part F — Real-world practice scenarios

Complete these scenarios in Databricks SQL Editor. Use only catalogs and
schemas that your instructor has approved for training.

## Scenario 1 — First-day workspace access

You have joined a data engineering team. Your lead asks you to confirm
that you can access the team's Databricks environment.

**Your tasks**

1. Sign in to the assigned workspace.
2. Open SQL Editor and locate Catalog Explorer.
3. Identify the catalogs visible to your account.
4. Record the active catalog and schema using `current_catalog()` and
   `current_schema()`.
5. Explain why access to a workspace does not automatically mean you
   have permission to use every catalog or schema.

**Expected evidence:** Record the workspace name, one visible catalog,
one visible schema, and the output of the context query. Do not share
workspace URLs or credentials publicly.

## Scenario 2 — Find the right data

A reporting analyst asks you to find the `customers` table. You do not
know which catalog or schema contains it.

**Your tasks**

1. Use Catalog Explorer to browse catalogs and schemas available to you.
2. Identify a schema that contains a `customers` table, if one is
   available.
3. Record the full object name in the form
   `catalog_name.schema_name.table_name`.
4. Explain why identifying the full object path is useful when several
   schemas contain tables with the same name.

If no `customers` table is available, record that the object was not
visible to your account. Do not create or access an unapproved object.

## Scenario 3 — Query in the correct context

You need to run a query against a table in the training environment.

**Your tasks**

1. Check the current catalog and schema.
2. Select the instructor-approved catalog and schema, if permitted.
3. Run `SELECT 1 AS connection_test;`.
4. Run the context query again.
5. If an approved sample table is available, query a few rows from it.
6. Explain how the current catalog and schema affect an unqualified
   table name.

## Scenario 4 — Access issue investigation

A classmate can see a catalog but receives a permission error when
trying to inspect a schema or query a table.

**Your tasks**

1. Check whether the catalog and schema are visible.
2. Note the exact operation that failed.
3. Distinguish a navigation issue from a permissions issue.
4. Capture the error text without exposing private workspace details.
5. Explain what information should be shared with the instructor or
   workspace administrator.

Do not attempt to bypass permissions or use another person's account.

## Scenario 5 — New team member handover

You are asked to prepare a short handover for another student who will
use the same Databricks workspace.

Include:

- Where to sign in.
- How to open SQL Editor.
- How to open Catalog Explorer.
- The catalog and schema approved for practice.
- The commands used to check the active context.
- One common access or context issue and how to report it.

## Scenario completion checklist

- [ ] I can navigate to SQL Editor and Catalog Explorer.
- [ ] I can identify catalogs and schemas visible to my account.
- [ ] I can check the active catalog and schema.
- [ ] I can identify a table's full three-part name.
- [ ] I can run a simple query and interpret the result.
- [ ] I know how to report a permissions issue safely.


**End of Video 2 practice.**
