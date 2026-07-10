# SQL Injection UNION Attack: Retrieving Data from Other Tables

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
* Data Extraction
* Database Enumeration
* Burp Suite
* SQL Query Analysis

---

# Overview

This lab demonstrates how to retrieve sensitive information from tables that are not originally queried by the application. After determining the correct number of columns and identifying a text-compatible column, an attacker can use a UNION-based SQL Injection attack to extract data from other database tables.

This technique is commonly used to retrieve usernames, passwords, API keys, or other sensitive information.

---

# Lab Objective

Exploit a UNION-based SQL Injection vulnerability to retrieve user credentials from another table in the database.

---

# Understanding the Vulnerability

Applications often execute queries against a single table, such as a products table.

However, when SQL Injection is possible, attackers can extend the query using the `UNION` operator to retrieve data from completely different tables, provided the injected query matches the original query's structure.

---

# Attack Methodology

1. Confirm SQL Injection.
2. Determine the correct number of columns.
3. Identify which columns support text values.
4. Locate the table containing user information.
5. Retrieve usernames and passwords using a UNION query.

---

# Solution Walkthrough

After identifying the correct column count and confirming which columns accepted text values, the users table was queried.

```http
GET /filter?category='+UNION+SELECT+username,password+FROM+users--
```

The application displayed the usernames and passwords stored in the database, including the administrator account.

The payload shown above is based on the methodology used in the lab while omitting environment-specific credentials.

---

# Payload Analysis

### `UNION SELECT`

Combines the application's original query with attacker-controlled data.

### `username, password`

Requests credential information from the target table.

### `FROM users`

Specifies the table containing user account information.

Because the number of columns and data types were already known, the injected query executed successfully.

---

# Why the Attack Works

The application directly incorporated user-controlled input into an SQL statement.

Since UNION-based SQL Injection was possible and the query structure had already been identified, arbitrary data could be retrieved from another table within the same database.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Confirming UNION-based SQL Injection.
* Identifying text-compatible columns.
* Looking for common tables such as `users`, `accounts`, or `customers`.
* Verifying whether sensitive information is reflected in the application's response.

---

# Common Mistakes

* Attempting to retrieve data before determining the correct column count.
* Forgetting to identify text-compatible columns.
* Assuming every application uses a table named `users`.
* Ignoring database permissions that may restrict access to certain tables.

---

# Real-World Scenario

Consider an online shopping application where the product page only queries product information.

If SQL Injection exists, an attacker could retrieve data from unrelated tables containing customer accounts, administrator credentials, or payment information, even though those tables are never accessed by the original application functionality.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Never concatenate user input into SQL statements.
* Apply the principle of least privilege to database accounts.
* Restrict unnecessary access to sensitive tables.
* Regularly perform secure code reviews and penetration testing.

---

# Key Takeaways

* UNION-based SQL Injection allows attackers to retrieve data from unrelated tables.
* Successful exploitation requires matching both the number of columns and their data types.
* Sensitive information such as usernames and passwords can be exposed if SQL Injection is present.
* Parameterized queries remain the most effective defense.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
