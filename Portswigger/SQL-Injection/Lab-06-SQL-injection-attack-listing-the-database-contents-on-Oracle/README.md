# SQL Injection Attack: Listing the Database Contents on Oracle

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* SQL Injection
* Oracle Database Enumeration
* UNION-based SQL Injection
* Oracle Data Dictionary
* Burp Suite
* Data Extraction

---

# Overview

This lab demonstrates how SQL Injection can be used to enumerate the structure of an **Oracle Database**. Unlike MySQL and PostgreSQL, Oracle stores metadata in its own **Data Dictionary**, requiring Oracle-specific tables and syntax during enumeration.

Understanding these differences is essential because SQL Injection payloads are often database-specific.

---

# Lab Objective

Exploit a SQL Injection vulnerability to enumerate Oracle database tables and columns, then retrieve the administrator's credentials.

---

# Understanding the Vulnerability

Oracle databases expose metadata through the **Data Dictionary**, a collection of system views that describe database objects.

Some commonly used views include:

* `ALL_TABLES` – Lists accessible tables.
* `ALL_TAB_COLUMNS` – Lists columns within tables.
* `DUAL` – A special one-row table used in Oracle queries.

If SQL Injection is possible, these views can be queried to discover the database structure before extracting sensitive data.

---

# Attack Methodology

1. Confirm UNION-based SQL Injection.
2. Enumerate Oracle tables.
3. Identify the users table.
4. Enumerate columns within the users table.
5. Retrieve usernames and passwords.

---

# Solution Walkthrough

### Step 1 — Enumerate Tables

Query Oracle's Data Dictionary to list available tables.

```http
GET /filter?category='+UNION+SELECT+table_name,NULL+FROM+all_tables--
```

---

### Step 2 — Enumerate Columns

After identifying the users table, enumerate its columns.

```http
GET /filter?category='+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='<USERS_TABLE>'--
```

---

### Step 3 — Extract Credentials

Query the discovered username and password columns.

```http
GET /filter?category=Pets'+UNION+SELECT+<USERNAME_COLUMN>,<PASSWORD_COLUMN>+FROM+<USERS_TABLE>--
```

The application returned the administrator's credentials, completing the lab. The payload sequence reflects the methodology used in the lab while replacing environment-specific table names, column names, and credentials with generic placeholders.

---

# Payload Analysis

### `ALL_TABLES`

Returns a list of tables accessible to the current database user.

### `ALL_TAB_COLUMNS`

Returns metadata about columns within accessible tables.

### `table_name`

Identifies database tables available for enumeration.

### `column_name`

Lists the columns belonging to a selected table.

### `UNION SELECT`

Combines the original query with attacker-controlled results.

---

# Why the Attack Works

The application constructed SQL queries by directly concatenating user input.

Once UNION-based SQL Injection was confirmed, Oracle's Data Dictionary could be queried just like ordinary tables, allowing an attacker to systematically discover the schema before extracting sensitive information.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Testing UNION-based SQL Injection.
* Querying Oracle-specific metadata views.
* Enumerating tables before attempting data extraction.
* Confirming discovered column names before retrieving data.

---

# Common Mistakes

* Using `information_schema` on Oracle instead of Oracle Data Dictionary views.
* Assuming table names are always identical across databases.
* Skipping schema enumeration and guessing column names.
* Forgetting that Oracle uses uppercase object names by default.

---

# Oracle vs Non-Oracle Enumeration

| Oracle                 | MySQL / PostgreSQL           |
| ---------------------- | ---------------------------- |
| `ALL_TABLES`           | `information_schema.tables`  |
| `ALL_TAB_COLUMNS`      | `information_schema.columns` |
| `DUAL` table available | No `DUAL` table required     |

Understanding these differences helps security testers adapt SQL Injection techniques across multiple database platforms.

---

# Real-World Scenario

During a security assessment, discovering that an application uses Oracle immediately changes the enumeration strategy.

Instead of querying `information_schema`, testers use Oracle Data Dictionary views to identify user tables, locate credential columns, and assess what sensitive information may be accessible through SQL Injection.

---

# Mitigation

Developers should:

* Use parameterized queries (prepared statements).
* Never concatenate user input into SQL statements.
* Restrict database privileges.
* Prevent unnecessary exposure of database metadata.
* Perform regular secure code reviews and penetration testing.

---

# Key Takeaways

* Oracle uses its own Data Dictionary instead of `information_schema`.
* Enumeration is a critical step before data extraction.
* Database-specific knowledge is essential for successful SQL Injection testing.
* Parameterized queries remain the strongest defense against SQL Injection.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* Oracle Database Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
