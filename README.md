
# SQL Learning Program — Business-Oriented SQL Practice

## 📘 Overview

This project is designed to help students learn SQL concepts through self-learning and practical business scenarios.

Students will watch the **Kudvenkat SQL Server Tutorial for Beginners** YouTube playlist and then practise the concepts using business datasets in Databricks SQL Editor.

### Learning approach

- Learn concepts through YouTube videos.
- Practise SQL independently.
- Solve real-world business problems.
- Progress from beginner to intermediate and advanced SQL.
- Work with multiple business datasets.

## 🎥 Learning Resources

- **YouTube lesson:** https://youtu.be/7GVFYt6_ZFM
- **Full playlist:** https://www.youtube.com/watch?v=7GVFYt6_ZFM&list=PL08903FB7ACA1C2FB
- **Practice environment:** Databricks SQL Editor

## ⚙️ Setup

Students will use Databricks SQL Editor for SQL practice.

**Important:** Skip playlist Video 1, which covers SQL Server and SQL Server Management Studio installation. The program uses Databricks SQL Editor instead.

## 📚 Learning Plan

| Batch | Videos | Topics |
|---|---|---|
| Batch 1 | 1–10 | Tables, constraints, identity columns, unique keys |
| Batch 2 | 11–18 | SELECT, GROUP BY, joins, NULL handling, UNION |
| Batch 3 | 19–22 | Stored procedures, output parameters, return values |
| Batch 4 | 23–30 | String, date/time, conversion, mathematical functions |
| Batch 5 | 31–35 | User-defined functions and temporary tables |
| Batch 6 | 36–42 | Indexes, views, updatable views, indexed views |

*Video 1 is skipped. Begin with Video 2 and follow the assigned playlist order.*

## 🏢 Business Datasets

SQL practice will use multiple business domains:

- **Retail:** Customers, products, stores, orders, and sales.
- **Banking:** Customers, accounts, transactions, and loans.
- **HR:** Employees, departments, salaries, and attendance.
- **Healthcare:** Synthetic patients, appointments, and billing records.

The datasets will support practical SQL exercises and testing scenarios.

## 🧪 Practice Methodology

For each learning batch:

1. Watch the assigned YouTube videos.
2. Understand the concepts and examples.
3. Write SQL queries independently.
4. Solve business-based questions.
5. Validate query results.
6. Test edge cases such as NULL values, duplicates, and unmatched records.
7. Save your SQL scripts for review.

## ⚠️ SQL Server vs. Databricks SQL

The YouTube playlist teaches SQL Server, while this project uses Databricks SQL.

Some syntax and features differ. For example:

- `IDENTITY` columns may require a Databricks-compatible alternative.
- Stored procedures and output parameters differ by platform and may not be supported in the same way.
- Temporary tables and indexed views have platform-specific limitations.
- Core concepts such as joins, filtering, grouping, and set operations remain useful, although syntax may vary.

**Rule:** Understand the concept from the video, then practise it using Databricks-compatible SQL.

## 📁 Suggested Repository Structure

```text
SQL-Learning/
├── README.md
├── guide/
│   └── SQL_Lesson_Learning_Guide_Videos_1-42.docx
├── datasets/
│   ├── retail/
│   ├── banking/
│   ├── hr/
│   └── healthcare/
└── practice/
    ├── batch-01-tables-constraints/
    ├── batch-02-querying-joins/
    ├── batch-03-procedures/
    ├── batch-04-functions/
    ├── batch-05-udfs-temp-tables/
    └── batch-06-indexes-views/
```

This is a suggested structure; folders can be added as the project develops.

## 🎯 Learning Objectives

By completing this program, students should be able to:

- Write SQL queries to retrieve and analyse data.
- Work with multiple tables and relationships.
- Apply filtering, grouping, aggregation, and joins.
- Use string, date, and mathematical functions.
- Understand constraints, functions, indexes, and views.
- Translate business requirements into SQL solutions.
- Validate query results and troubleshoot common issues.

## ✅ Progress Checklist

- [ ] Complete the assigned videos, skipping Video 1.
- [ ] Practise each learning batch.
- [ ] Solve business scenarios using multiple datasets.
- [ ] Validate results and test edge cases.
- [ ] Complete the testing scenarios.
- [ ] Review and improve SQL solutions.

---

**Project status:** Learning guide and practice framework.

**Next phase:** Develop structured testing scenarios from beginner to advanced difficulty.
