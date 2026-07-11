# Visible Error-Based SQL Injection

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* Error-Based SQL Injection
* Information Disclosure
* Burp Suite Repeater
* SQL Type Casting
* Database Enumeration
* Credential Extraction

---

# Overview

This lab demonstrates **Visible Error-Based SQL Injection**, where the application leaks database information directly through SQL error messages.

Unlike Blind SQL Injection, attackers do not need to infer information through page behavior or time delays. Instead, the database itself exposes sensitive information inside error messages, making data extraction significantly faster.

---

# Lab Objective

Exploit a visible error-based SQL Injection vulnerability to retrieve the administrator's credentials.

---

# Understanding the Vulnerability

Some applications return detailed database errors to the client.

If user-controlled input is incorporated into an SQL query, attackers can intentionally trigger errors containing the results of database queries.

This disclosure can reveal usernames, passwords, database structure, and other sensitive information.

---

# Attack Methodology

1. Confirm SQL Injection.
2. Verify that SQL comments work.
3. Test SQL type casting.
4. Extract the administrator username.
5. Extract the administrator password.
6. Log in as the administrator.

---

# Solution Walkthrough

## Step 1 – Capture the Request

Open the lab in **Burp Browser**.

Locate the request containing the `TrackingId` cookie in **Proxy → HTTP History**.

Send the request to **Repeater**.

---

## Step 2 – Confirm SQL Injection

Append a single quote to the cookie value.

```http
TrackingId=abc123'
```

**Observation**

A SQL error appears, confirming that the cookie is vulnerable.

---

## Step 3 – Verify SQL Comments

Modify the payload.

```http
TrackingId=abc123'--
```

**Observation**

The error disappears, confirming that the SQL comment successfully terminates the remaining query.

---

## Step 4 – Test SQL Type Casting

Use:

```http
TrackingId=' AND 1=CAST((SELECT 1) AS int)--
```

**Observation**

The query executes successfully, confirming that the payload syntax is valid.

---

## Step 5 – Extract the Username

Modify the payload.

```http
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```

**Observation**

The database error leaks the administrator username because it cannot convert a text value into an integer.

---

## Step 6 – Extract the Password

Use:

```http
TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

**Observation**

The database error reveals the administrator password. The actual password has been replaced with `<ADMINISTRATOR_PASSWORD>` in this documentation.

---

## Step 7 – Log In

Use the extracted administrator credentials to authenticate and solve the lab.

---

# Payload Analysis

### `CAST(... AS int)`

Attempts to convert the returned value into an integer.

### `SELECT username FROM users`

Retrieves the administrator username.

### `SELECT password FROM users`

Retrieves the administrator password.

### `LIMIT 1`

Restricts the query to a single row, preventing errors caused by multiple returned values.

---

# Why the Attack Works

The application exposes raw database error messages to users.

When text values such as usernames or passwords are forced into an integer conversion, the database includes the original text in the error message, unintentionally leaking sensitive information.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Appending special characters such as `'`.
* Looking for verbose SQL error messages.
* Testing SQL comments (`--`).
* Using type conversion functions to trigger informative errors.

---

# Common Mistakes

* Ignoring verbose error messages.
* Forgetting to limit queries to a single row.
* Using the wrong data type during casting.
* Assuming visible SQL errors are harmless.

---

# Real-World Scenario

Many production systems accidentally expose detailed database errors during development or debugging.

Attackers can exploit these messages to extract sensitive information without needing blind inference techniques, making visible error-based SQL Injection one of the quickest forms of SQL Injection to exploit.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Disable detailed database error messages in production.
* Validate user input.
* Apply least-privilege database permissions.
* Perform regular penetration testing.

---

# Key Takeaways

* Visible database errors can directly expose sensitive information.
* Type conversion errors are useful for extracting query results.
* Error messages should never be exposed to end users.
* Parameterized queries effectively prevent this vulnerability.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
