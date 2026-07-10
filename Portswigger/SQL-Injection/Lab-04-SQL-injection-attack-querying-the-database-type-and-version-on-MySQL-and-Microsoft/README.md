# SQL Injection Attack: Querying the Database Type and Version on MySQL and Microsoft SQL Server

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* SQL Injection
* Database Fingerprinting
* MySQL
* Microsoft SQL Server
* UNION-based SQL Injection
* Burp Suite

---

# Overview

This lab demonstrates how SQL Injection can be used to identify the underlying database management system (DBMS) and retrieve its version information on **MySQL** and **Microsoft SQL Server (MSSQL)**.

Identifying the database type is one of the first objectives after discovering an SQL Injection vulnerability. Since every DBMS has its own syntax, system tables, and built-in functions, fingerprinting the database allows attackers to craft payloads that are compatible with the target.

---

# Lab Objective

Exploit a SQL Injection vulnerability to determine the database type and retrieve its version information from a MySQL or Microsoft SQL Server database.

---

# Understanding the Vulnerability

Different database engines expose version information differently.

Unlike Oracle, which requires querying system views such as `v$version`, both MySQL and Microsoft SQL Server provide a built-in variable named `@@version`.

This makes version enumeration much simpler once a UNION-based SQL Injection is confirmed.

---

# Attack Methodology

1. Confirm that the application is vulnerable to SQL Injection.
2. Identify the number of columns returned by the query.
3. Verify that a UNION query is accepted.
4. Retrieve version information using a database-specific variable.
5. Identify the underlying database platform.

---

# Solution Walkthrough

The vulnerable parameter was the `category` parameter.

First, a UNION query was used to confirm that two columns could be returned.

```http
GET /filter?category=Pets'+UNION+SELECT+'abc','def'#
```

After confirming the query structure, the built-in version variable was queried.

```http
GET /filter?category=Pets'+UNION+SELECT+@@version,NULL#
```

The application displayed the database version, confirming the underlying DBMS. These payloads are based on the solution used during the lab.

---

# Payload Analysis

### `UNION SELECT`

Combines the original query with attacker-controlled output.

### `@@version`

A built-in system variable available in both MySQL and Microsoft SQL Server that returns version information about the running database.

### `#`

Acts as a comment character in MySQL, causing the remainder of the original SQL query to be ignored.

---

# Why the Attack Works

The application directly concatenated user input into the SQL statement without parameterized queries.

This allowed the injected UNION query to execute successfully and return information from the database engine itself.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Testing whether UNION queries are accepted.
* Determining the correct number of returned columns.
* Trying database-specific variables such as `@@version`.
* Looking for database version information in the application's response.

---

# Real-World Scenario

During a penetration test, identifying the database type helps determine which SQL syntax and exploitation techniques should be used next.

For example, knowing the target is MySQL instead of Oracle changes which system tables, functions, and payloads are available for enumeration and data extraction.

---

# Oracle vs MySQL/Microsoft SQL Server

| Oracle                        | MySQL / Microsoft SQL Server          |
| ----------------------------- | ------------------------------------- |
| Uses `FROM dual`              | No `dual` table required              |
| Version stored in `v$version` | Version available through `@@version` |
| Oracle-specific syntax        | MySQL/MSSQL-specific syntax           |

Understanding these differences allows security testers to adapt their SQL Injection techniques across different database platforms.

---

# Mitigation

Developers should:

* Use parameterized queries (prepared statements).
* Avoid dynamic SQL query construction.
* Restrict unnecessary database permissions.
* Suppress database error messages from users.
* Regularly perform secure code reviews and security testing.

---

# Key Takeaways

* Database fingerprinting is an important reconnaissance step.
* Different database engines expose version information differently.
* MySQL and Microsoft SQL Server provide the `@@version` variable.
* Understanding DBMS-specific syntax is essential during SQL Injection testing.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* MySQL Documentation
* Microsoft SQL Server Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
