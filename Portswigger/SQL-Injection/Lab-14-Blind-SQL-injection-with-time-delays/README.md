# Blind SQL Injection with Time Delays

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
* PostgreSQL
* Response Time Analysis
* SQL Injection Verification

---

# Overview

This lab demonstrates **Time-Based Blind SQL Injection**, where the application provides neither visible query results nor database errors.

Instead of observing page content, attackers determine whether injected SQL statements execute successfully by measuring how long the server takes to respond.

---

# Lab Objective

Exploit a Blind SQL Injection vulnerability by triggering a deliberate time delay in the database.

---

# Understanding the Vulnerability

Some applications suppress both SQL errors and query results, leaving attackers with no visible indication that their payload executed.

In these situations, database-specific delay functions such as `pg_sleep()` can be used.

If the application pauses for a predictable amount of time, it confirms that the injected SQL statement was successfully executed.

---

# Attack Methodology

1. Capture the request containing the vulnerable cookie.
2. Send the request to Burp Suite Repeater.
3. Inject a database delay function.
4. Measure the server's response time.
5. Confirm SQL Injection based on the observed delay.

---

# Solution Walkthrough

## Step 1 – Capture the Request

Open the lab in Burp Browser.

Locate the request containing the `TrackingId` cookie and send it to **Burp Suite Repeater**.

---

## Step 2 – Inject a Time Delay

Modify the `TrackingId` cookie as follows:

```http
TrackingId=x'||pg_sleep(10)--
```

Send the request.

**Observation**

The application delays its response by approximately **10 seconds**, confirming that the injected SQL statement is executed by the database.

---

# Payload Analysis

### `pg_sleep(10)`

A PostgreSQL function that pauses query execution for 10 seconds.

### `||`

The string concatenation operator used to append the injected payload.

### `--`

Comments out the remaining portion of the original SQL query.

---

# Why the Attack Works

Although the application does not reveal SQL errors or query results, it still executes the injected SQL statement.

The attacker confirms successful execution by measuring the delay introduced by the `pg_sleep()` function.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Measuring unusual increases in response time.
* Testing database-specific delay functions.
* Comparing normal and delayed requests.
* Repeating requests to eliminate network latency as a cause.

---

# Common Mistakes

* Confusing network latency with intentional SQL delays.
* Using delay functions that do not match the target database.
* Sending multiple concurrent requests, making timing difficult to interpret.
* Assuming no visible output means the application is secure.

---

# Real-World Scenario

Many production applications suppress SQL errors and return identical pages for every request.

Time-based SQL Injection provides a reliable way to verify SQL Injection in these environments by using response time as the communication channel instead of page content.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Never concatenate user input into SQL statements.
* Restrict database permissions.
* Monitor for abnormal query execution times.
* Perform regular SQL Injection testing.

---

# Key Takeaways

* Time delays can confirm SQL Injection even when no output is visible.
* `pg_sleep()` is commonly used on PostgreSQL databases.
* Response time becomes the attacker's feedback mechanism.
* Parameterized queries effectively prevent this vulnerability.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* PostgreSQL Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
