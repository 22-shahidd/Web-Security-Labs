# SQL Injection UNION Attack: Finding a Column Containing Text

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
* Column Type Identification
* Burp Suite
* SQL Query Analysis
* Database Enumeration

---

# Overview

This lab demonstrates how to identify which columns in a SQL query are capable of displaying **text data**. After determining the number of returned columns, the next step in a UNION-based SQL Injection attack is to discover where string values can be successfully injected.

This information is essential because sensitive data such as usernames, email addresses, and passwords are typically stored as text.

---

# Lab Objective

Identify a column that accepts and displays text values using a UNION-based SQL Injection attack.

---

# Understanding the Vulnerability

A successful `UNION SELECT` attack requires:

* The correct number of columns.
* Compatible data types.

Even if the column count is correct, inserting a string into an integer column (or vice versa) may result in an error.

Attackers therefore test each column individually until they find one capable of displaying text.

---

# Attack Methodology

1. Determine the correct number of columns.
2. Construct a UNION SELECT statement.
3. Replace one `NULL` value with a known text string.
4. Observe the application's response.
5. Repeat until the text appears in the response.

---

# Solution Walkthrough

The lab first confirmed the correct number of returned columns.

A test string was then inserted into one column at a time.

```http
GET /filter?category='+UNION+SELECT+'RANDOM_TEXT',NULL,NULL--
```

If the application returned an error, the text value was moved to the next column.

Once the random text appeared in the application's response, that column was confirmed to support text values.

This methodology matches the approach used in the lab while replacing the lab-specific random string with a generic placeholder.

---

# Payload Analysis

### `UNION SELECT`

Combines the injected query with the original query.

### `NULL`

Maintains compatibility with unknown column data types.

### `'RANDOM_TEXT'`

A unique string used to identify which column is capable of displaying textual data.

By observing where this value appears, an attacker can determine which column is suitable for extracting usernames, passwords, or other string-based information.

---

# Why the Attack Works

The application directly inserted user input into the SQL query.

Once the correct number of columns had been identified, placing a unique string into each column one at a time allowed the attacker to determine which column accepted text values.

This information is required before extracting meaningful data from other tables.

---

# Detection Tips

Security testers can identify this behavior by:

* Confirming the correct number of returned columns.
* Testing one text value per column.
* Looking for reflected text in the application's response.
* Observing type mismatch errors.

---

# Common Mistakes

* Testing multiple text values simultaneously.
* Forgetting the correct column count.
* Using the same string repeatedly without checking where it appears.
* Ignoring database type mismatch errors.

---

# Real-World Scenario

Suppose an attacker wants to retrieve usernames from an application's database.

Before extracting any data, they must first identify which column in the original query can display text values. Without this step, subsequent UNION-based payloads may fail due to incompatible data types.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Avoid dynamically building SQL statements.
* Validate user input.
* Restrict database permissions.
* Perform regular SQL Injection testing.

---

# Key Takeaways

* Matching data types is just as important as matching the number of columns.
* Text-compatible columns are required to extract usernames, passwords, and other string data.
* Testing columns systematically is more reliable than guessing.
* Understanding column data types is a key step in UNION-based SQL Injection.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
