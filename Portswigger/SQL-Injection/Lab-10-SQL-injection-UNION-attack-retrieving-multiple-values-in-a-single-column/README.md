# SQL Injection UNION Attack: Retrieving Multiple Values in a Single Column

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
* Data Concatenation
* Database Enumeration
* Burp Suite
* SQL Query Analysis

---

# Overview

This lab demonstrates how attackers can retrieve multiple pieces of information through a **single output column** using string concatenation.

In many real-world applications, only one column in the original query is capable of displaying text. Instead of retrieving one value at a time, attackers combine multiple database values into a single string, allowing usernames, passwords, or other sensitive information to be displayed together.

---

# Lab Objective

Exploit a UNION-based SQL Injection vulnerability to retrieve multiple values from another table by concatenating them into a single output column.

---

# Understanding the Vulnerability

UNION-based SQL Injection requires the injected query to match both the number and data types of the original query.

If only one column supports text output, attackers can concatenate multiple database fields into one string before returning the result.

The exact concatenation operator depends on the database management system:

* Oracle → `||`
* PostgreSQL → `||`
* MySQL → `CONCAT()`
* Microsoft SQL Server → `+`

Understanding database-specific syntax is essential for successful exploitation.

---

# Attack Methodology

1. Confirm UNION-based SQL Injection.
2. Determine the correct number of columns.
3. Identify the text-compatible column.
4. Combine multiple database fields into a single string.
5. Retrieve the concatenated output through the application's response.

---

# Solution Walkthrough

After identifying the text-compatible column, the username and password fields were concatenated into a single value.

```http id="8zvpl6"
GET /filter?category='+UNION+SELECT+username||':'||password,NULL+FROM+users--
```

The application displayed both values together in one column, allowing sensitive information to be extracted even though only a single text column was available.

The payload above illustrates the technique while avoiding lab-specific credentials.

---

# Payload Analysis

### `UNION SELECT`

Combines the injected query with the application's original query.

### `||`

The Oracle and PostgreSQL string concatenation operator.

### `':'`

A separator inserted between the username and password to improve readability.

### `username || ':' || password`

Combines multiple database fields into one text value before returning the result.

---

# Why the Attack Works

Although the original query only exposed one text column, SQL allows multiple string values to be merged together before being returned.

By concatenating database fields, attackers can extract multiple pieces of sensitive information through a single output column.

---

# Detection Tips

Security testers can identify this technique by:

* Confirming that only one text column is available.
* Testing database-specific concatenation operators.
* Looking for combined output in the application's response.
* Comparing responses across different database platforms.

---

# Common Mistakes

* Using the wrong concatenation operator for the target database.
* Forgetting that different DBMS products use different syntax.
* Returning multiple columns when only one text column exists.
* Omitting separators, making extracted data difficult to interpret.

---

# Real-World Scenario

Imagine a customer portal that displays only one text field within the application's response.

If SQL Injection exists, an attacker can concatenate usernames, email addresses, API keys, or passwords into a single output string, enabling sensitive information to be extracted despite the application's display limitations.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Avoid constructing SQL statements using user input.
* Restrict unnecessary database privileges.
* Perform regular SQL Injection testing.
* Monitor applications for abnormal query behavior.

---

# Key Takeaways

* A single output column does not prevent data extraction.
* String concatenation allows multiple values to be returned together.
* Concatenation syntax differs across database platforms.
* Understanding DBMS-specific features improves SQL Injection testing.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* Oracle Database Documentation
* PostgreSQL Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
