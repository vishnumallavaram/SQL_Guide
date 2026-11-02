# Batch 1 --- Video 3: Creating, Altering, and Dropping a Catalog

## Learning objectives

By completing this exercise, students will practise and understand:

-   The purpose of database lifecycle commands.
-   How the SQL Server concept of a database maps to the catalog level
    in this course.
-   Creating a catalog in Databricks.
-   Understanding catalog alteration and the permissions required.
-   Understanding the impact of dropping a catalog.
-   Recognising and avoiding destructive operations.

## Learning approach

The assigned tutorial demonstrates creating, altering, and dropping a
database in SQL Server.

Watch the video to understand the purpose of database lifecycle
operations and the consequences of changing or deleting a database.

**Practical work for this course must be completed in Databricks SQL
Editor.** For this learning module, use the Databricks catalog level as
the closest structural concept to the SQL Server database discussed in
the video.

> **Important terminology:** Databricks uses the hierarchy **catalog →
> schema → table**. A catalog is not identical in every respect to a SQL
> Server database. This course uses catalog-level operations to teach
> the corresponding organizational and lifecycle concepts.

## Environment

## Databricks links

- [Open Databricks](https://www.databricks.com/) — use your course or organization workspace URL to sign in.
- [Create catalogs](https://learn.microsoft.com/en-us/azure/databricks/catalogs/create-catalog) — official catalog creation guidance.
- [ALTER CATALOG reference](https://learn.microsoft.com/en-us/azure/databricks/sql/language-manual/sql-ref-syntax-ddl-alter-catalog) — supported catalog alteration syntax.
- [DROP CATALOG reference](https://learn.microsoft.com/en-us/azure/databricks/sql/language-manual/sql-ref-syntax-ddl-drop-catalog) — deletion syntax and behavior.

> Your actual workspace address is specific to your account or organization. Use the workspace URL supplied by your instructor. Review the official DROP CATALOG guidance before any deletion exercise.


Use **Databricks SQL Editor**.

Catalog creation, alteration, and deletion depend on the workspace
configuration, Unity Catalog setup, and the permissions granted to your
user. Use only a training catalog that you are authorized to manage.

## Safety rules

Before running any command that changes or deletes an object:

1.  Read the full statement.
2.  Confirm the exact catalog name.
3.  Confirm that it is a training object created for this exercise.
4.  Check whether it contains schemas, tables, or other objects.
5.  Never run a `DROP CATALOG` statement against a shared, production,
    or instructor-managed catalog.
6.  If you do not have the required permissions, stop and ask the
    instructor. Do not try the command against another catalog.

## Part A --- Understand the catalog hierarchy

Review this hierarchy:

``` text
Databricks workspace
    └── Catalog
          └── Schema
                └── Table
```

Answer these questions:

-   What does a catalog contain?
-   What does a schema contain?
-   Where are tables created?
-   Why should teams organize objects into catalogs and schemas?
-   Why is it important to confirm the active catalog and schema before
    running SQL?

## Part B --- Inspect available catalogs

Run:

``` sql
SHOW CATALOGS;
```

Choose a catalog that you are allowed to inspect. List its schemas:

``` sql
SHOW SCHEMAS IN <catalog_name>;
```

Replace `<catalog_name>` with the actual catalog name. Do not type the
angle brackets.

Record the catalog names you can see and identify which one is
designated for training, if one exists.

## Part C --- Create a training catalog

Use a unique training catalog name approved by your instructor. For
example:

``` sql
CREATE CATALOG batch1_training;
```

If the name already exists, do not drop or replace it. Ask the
instructor for another name or use the existing training catalog only if
you have been told to do so.

Verify the result:

``` sql
SHOW CATALOGS;
```

## Part D --- Understand catalog alteration

The tutorial introduces `ALTER DATABASE`. In Databricks, catalog
alteration is performed with catalog-specific syntax and is subject to
supported operations and permissions.

Students should understand that alteration changes metadata or settings
of an existing catalog; it does not mean the catalog is recreated.

Before attempting an alteration:

1.  Identify the exact catalog to be changed.
2.  Check the Databricks SQL syntax supported by your workspace.
3.  Confirm that your user has the required privilege.
4.  Make only the instructor-approved change to the training catalog.
5.  Verify the result after the change.

### Practice task

Ask your instructor to assign one supported catalog alteration for the
training environment. Record:

-   The catalog being changed.
-   The property or metadata being changed.
-   The permission required.
-   The statement used.
-   How you verified the change.

> Do not guess an `ALTER CATALOG` clause. Supported catalog alteration
> operations can depend on the Databricks feature and workspace
> configuration.

## Part E --- Understand dropping a catalog

Dropping a catalog is a destructive operation. It can remove the catalog
and its contained objects, depending on the command and the catalog's
contents.

Review the command form:

``` sql
DROP CATALOG <catalog_name>;
```

Do not execute this placeholder statement.

For a controlled practice, the instructor should first confirm that the
training catalog is disposable, that its contents can be deleted, and
that the student has the required permission. Only then should the
instructor provide the exact command and execution steps.

### Questions

-   What happens when a catalog is dropped?
-   Why should a catalog name be checked before deletion?
-   Why must shared and production catalogs never be used for this
    exercise?
-   What should you do if the catalog contains objects you need to
    retain?
-   Why is deletion different from changing catalog metadata?

## Part F --- Practice tasks

1.  List the catalogs available to your user.
2.  Inspect the schemas in an approved catalog.
3.  Create a new catalog using an instructor-approved training name.
4.  Verify that the catalog was created.
5.  Describe one supported catalog alteration and perform it only if the
    instructor approves and your permissions allow it.
6.  Explain the effect of dropping a catalog.
7.  Do not drop the training catalog unless the instructor explicitly
    authorizes the deletion.

## Part G --- Validation checklist

-   [ ] I can explain the catalog → schema → table hierarchy.
-   [ ] I can list catalogs visible to my user.
-   [ ] I can list schemas in a catalog I can access.
-   [ ] I can create an approved training catalog, if permitted.
-   [ ] I can verify that the catalog exists.
-   [ ] I understand that catalog alteration requires supported syntax
    and permissions.
-   [ ] I understand that dropping a catalog is destructive.
-   [ ] I can identify why shared and production catalogs must be
    protected.

## Key learning points

-   `CREATE CATALOG` creates a catalog in Databricks, subject to
    permissions and workspace configuration.
-   Catalogs organize schemas and the objects within them.
-   Catalog alteration uses Databricks-specific supported syntax; do not
    assume SQL Server `ALTER DATABASE` syntax will work.
-   `DROP CATALOG` is destructive and must be used only with explicit
    authorization on a disposable training catalog.
-   Always verify the target object and its contents before making
    structural changes.


------------------------------------------------------------------------

# Part H — Real-world practice scenarios

These scenarios focus on decisions a data engineer should make before
changing catalog-level objects. Use only instructor-approved training
catalogs. Do not perform destructive actions on shared or production
objects.

## Scenario 1 — New project onboarding

A new project named `RetailAnalytics` is starting. The team needs a
separate catalog to organize its data objects.

**Your tasks**

1. Check whether the proposed catalog name already exists.
2. Confirm with the instructor which catalog name should be used.
3. Create the catalog only if you have approval and the required
   permission.
4. Verify that it appears in the catalog list.
5. Explain why a project may benefit from a separate catalog.

Use the create statement from Part C, replacing the example name only
with the approved name.

## Scenario 2 — Organize a project into schemas

The project team wants to organize data into `bronze`, `silver`, and
`gold` schemas inside its approved training catalog.

**Your tasks**

1. Inspect the schemas that already exist in the approved catalog.
2. Identify which requested schemas are missing.
3. Ask the instructor whether you are authorized to create the missing
   schemas.
4. If authorized, create only the missing schemas using Databricks SQL.
5. Verify the resulting catalog → schema hierarchy.

Do not create duplicate schemas or modify a catalog that belongs to
another team.

## Scenario 3 — A project setting needs to change

The project owner requests a change to one catalog property or setting.

**Your tasks**

1. Identify the exact catalog and the requested property.
2. Check the official `ALTER CATALOG` documentation linked above.
3. Confirm that the requested operation is supported in your workspace.
4. Confirm the required permission with the instructor or administrator.
5. Record the approved SQL statement before execution.
6. Execute only after approval, then verify the result.

**Discussion:** Why should you not copy an `ALTER DATABASE` statement
from the SQL Server tutorial and assume it will work in Databricks?

## Scenario 4 — Catalog deletion request

A project owner says that an old training catalog is no longer needed
and asks you to delete it.

**Your tasks**

1. Do not execute a DROP statement immediately.
2. Confirm the exact catalog name with the requester.
3. Confirm that it is a disposable training catalog and not shared or
   production.
4. Ask the instructor to verify whether any schemas, tables, or data
   must be retained.
5. Review the official `DROP CATALOG` behavior and required privileges.
6. Write down the proposed command for review.
7. Execute only if the instructor explicitly authorizes the deletion.

**Discussion:** What could go wrong if the catalog name is mistyped or
the catalog contains data another team still needs?

## Scenario 5 — Permission and ownership issue

You can list a catalog, but you cannot create a schema or alter the
catalog.

**Your tasks**

1. Record the catalog name and the operation that failed.
2. Capture the permission error.
3. Identify whether the task requires a privilege you do not have.
4. Report the issue to the instructor or administrator.
5. Explain why you should not try to work around the permission model.

## Scenario 6 — Change review

You are reviewing a teammate's proposed catalog change.

They propose to create a catalog, change a catalog setting, and then
drop the catalog as part of cleanup.

**Your tasks**

1. Separate the proposal into create, alter, and drop operations.
2. Identify the approval and permission needed for each operation.
3. Identify which operation is destructive.
4. Define what must be checked before deletion.
5. Recommend a safe review and approval sequence.

## Scenario completion checklist

- [ ] I can assess whether a new catalog is needed.
- [ ] I can inspect the catalog and its schemas before making changes.
- [ ] I know to verify supported ALTER syntax rather than guessing.
- [ ] I can identify the risks and approvals required before DROP.
- [ ] I can report permission issues without attempting to bypass them.
- [ ] I can explain the difference between creating, altering, and
  dropping a catalog.

> These scenarios are decision-making and controlled practice
> exercises. Any CREATE, ALTER, or DROP operation must follow the
> workspace administrator's permissions and instructor approval.


**End of Video 3 practice.**
