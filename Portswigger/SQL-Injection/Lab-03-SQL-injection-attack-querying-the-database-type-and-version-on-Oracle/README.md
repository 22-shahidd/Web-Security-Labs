# SQL Injection Attack: Querying the Database Type and Version on Oracle

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
* Oracle Database
* UNION-based SQL Injection
* Burp Suite
* SQL Enumeration

---

# Overview

This lab demonstrates how SQL Injection can be used to identify the underlying database management system (DBMS) and retrieve its version information.

Before extracting sensitive data, attackers often fingerprint the database to determine which SQL syntax, system tables, and database-specific functions are available. Since different DBMS products implement SQL differently, identifying the database type is an important reconnaissance step.

---

# Lab Objective

Exploit a SQL Injection vulnerability to determine that the application is running Oracle Database and retrieve its version information.

---

# Understanding the Vulnerability

Applications vulnerable to SQL Injection often allow attackers to append additional SQL queries using the `UNION` operator.

Before retrieving useful information, an attacker typically answers two questions:

1. Which database is being used?
2. What version of the database is running?

Knowing the DBMS helps attackers craft payloads that are compatible with that specific database engine.

---

# Attack Methodology

1. Confirm that the application is vulnerable to SQL Injection.
2. Determine the number of columns returned by the original query.
3. Verify that a UNION query is accepted.
4. Identify Oracle-specific syntax.
5. Retrieve the Oracle database version.

---

# Solution Walkthrough

The vulnerable parameter was the `category` parameter.

The first payload verified that a UNION query with two columns was accepted.

```http
GET /filter?category=Pets'+UNION+SELECT+'abc','def'+FROM+dual--
```

Once the correct number of columns was confirmed, the Oracle system view containing version information was queried.

```http
GET /filter?category=Pets'+UNION+SELECT+BANNER,NULL+FROM+v$version--
```

The application returned the database banner, confirming both the database type and its version. The payloads are based on the solution used during the lab.

---

# Payload Analysis

### `UNION SELECT`

Combines the results of the original query with attacker-controlled data.

### `FROM dual`

`dual` is a special one-row table provided by Oracle. It is commonly used when a query requires a `FROM` clause but no actual table data.

### `v$version`

A dynamic performance view that stores Oracle version information.

### `BANNER`

Returns descriptive information about the Oracle database version.

---

# Why the Attack Works

The application directly inserted user input into the SQL query without parameterization.

Because the database accepted the injected `UNION SELECT` statement, Oracle system views could be queried just like regular tables.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Testing whether `UNION SELECT` statements are accepted
* Identifying the correct number of returned columns
* Looking for Oracle-specific behavior such as the requirement to query from `dual`
* Observing application responses for database version information

---

# Real-World Scenario

Imagine a penetration tester assessing an enterprise application.

Before attempting advanced SQL Injection techniques, the tester identifies that the backend uses Oracle Database. This information allows them to use Oracle-specific tables, functions, and syntax during further testing, significantly increasing the likelihood of successful exploitation.

---

# Mitigation

Developers should:

* Use parameterized queries (prepared statements)
* Avoid dynamically constructing SQL statements
* Restrict database permissions
* Prevent database error information from being exposed
* Regularly test applications for SQL Injection vulnerabilities

---

# Key Takeaways

* Database fingerprinting is often the first step after identifying SQL Injection.
* Oracle uses database-specific objects such as `dual` and `v$version`.
* Knowing the DBMS allows attackers to craft more effective payloads.
* Parameterized queries remain the most effective defense against SQL Injection.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* Oracle Database Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
