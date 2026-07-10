# SQL Injection UNION Attack: Determining the Number of Columns Returned by the Query

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* SQL Injection
* UNION-based SQL Injection
* Column Enumeration
* Burp Suite
* SQL Query Analysis
* Database Reconnaissance

---

# Overview

This lab demonstrates one of the most important prerequisites for a successful **UNION-based SQL Injection** attack: determining how many columns are returned by the original SQL query.

Before attackers can retrieve arbitrary data using `UNION SELECT`, the injected query must match the structure of the original query. If the number of columns does not match, the database rejects the request with an error.

---

# Lab Objective

Determine the exact number of columns returned by the application's SQL query using a UNION-based SQL Injection attack.

---

# Understanding the Vulnerability

The SQL `UNION` operator combines the results of two or more `SELECT` statements.

For the UNION operation to succeed:

* Both queries must return the same number of columns.
* The corresponding columns must contain compatible data types.

Determining the correct column count is therefore one of the first tasks during UNION-based SQL Injection testing.

---

# Attack Methodology

1. Confirm that the application is vulnerable to SQL Injection.
2. Inject a UNION SELECT statement.
3. Begin with a small number of `NULL` values.
4. Increase the number of `NULL` values until the database accepts the query.
5. Record the number of returned columns for future exploitation.

---

# Solution Walkthrough

The vulnerable `category` parameter was modified using a UNION query.

A payload containing `NULL` values was injected.

```http
GET /filter?category=' UNION SELECT NULL,NULL,NULL--
```

If an error occurred, additional `NULL` values would be added or removed until the query executed successfully.

Once the response no longer produced an error, the correct number of returned columns had been identified. This approach reflects the methodology used in the lab.

---

# Payload Analysis

### `UNION`

Combines the results of two SQL queries.

### `SELECT`

Specifies the values returned by the injected query.

### `NULL`

Used because it is compatible with most SQL data types, making it ideal for determining the number of columns.

### `--`

Comments out the remainder of the original SQL statement to prevent syntax errors.

---

# Why the Attack Works

The database requires both SELECT statements within a UNION query to have identical structures.

By repeatedly adjusting the number of `NULL` values, an attacker can identify the exact number of columns expected by the original query.

This information becomes the foundation for all subsequent UNION-based SQL Injection attacks.

---

# Detection Tips

Security testers can identify this behavior by:

* Testing whether UNION SELECT statements are accepted.
* Incrementally adjusting the number of `NULL` values.
* Observing database errors disappear when the correct column count is reached.
* Comparing application responses after each request.

---

# Common Mistakes

* Guessing the number of columns instead of testing systematically.
* Using literal values instead of `NULL`, causing data type mismatches.
* Forgetting to comment out the remainder of the original SQL query.
* Assuming every application returns the same number of columns.

---

# Real-World Scenario

During a penetration test, an attacker discovers that an application is vulnerable to SQL Injection.

Before attempting to extract sensitive information, they first determine how many columns the original query returns. This enables them to build valid UNION payloads for database enumeration and data extraction.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Prevent SQL Injection by avoiding dynamic SQL construction.
* Hide database error messages from users.
* Validate user input on the server side.
* Perform regular security testing.

---

# Key Takeaways

* Determining the correct number of columns is the first step in UNION-based SQL Injection.
* `NULL` values are commonly used because they are compatible with most SQL data types.
* Matching the query structure is essential for successful UNION attacks.
* UNION-based SQL Injection relies on understanding how SQL queries are constructed.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
