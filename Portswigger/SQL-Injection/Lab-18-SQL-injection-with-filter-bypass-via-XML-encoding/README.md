# SQL Injection with Filter Bypass via XML Encoding

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* SQL Injection
* WAF Bypass
* XML Encoding
* Hackvertor
* Burp Suite Repeater
* Data Extraction

---

# Overview

This lab demonstrates how attackers can bypass Web Application Firewall (WAF) filters using **XML encoding**. Instead of sending a plain SQL Injection payload, the payload is encoded as XML entities so that security filters fail to detect it. The server later decodes the payload before executing the SQL query.

---

# Lab Objective

Exploit a SQL Injection vulnerability protected by a WAF by encoding the payload with XML entities and retrieve the administrator's credentials.

---

# Understanding the Vulnerability

Some applications filter common SQL keywords such as `UNION` or `SELECT`.

When the request body is processed as XML, encoded entities may bypass these filters because the WAF inspects the encoded payload while the server later decodes it before executing the SQL statement.

This mismatch allows attackers to evade input filtering.

---

# Attack Methodology

1. Capture the stock check request.
2. Verify that user input is processed.
3. Confirm that the WAF blocks a normal SQL Injection payload.
4. Encode the payload using Hackvertor.
5. Determine the number of returned columns.
6. Retrieve the administrator credentials.

---

# Solution Walkthrough

## Step 1 – Capture the Request

Open the product page.

Click **Check stock**.

Capture the following request in Burp Suite and send it to **Repeater**.

```http
POST /product/stock
```

The request body contains:

```xml
<stockCheck>
    <productId>1</productId>
    <storeId>1</storeId>
</stockCheck>
```

---

## Step 2 – Verify User Input

Modify the request.

```xml
<storeId>1+1</storeId>
```

**Observation**

The application returns stock information for **Store 2**, confirming that the input is processed by the backend.

---

## Step 3 – Test a UNION Injection

Attempt a normal payload.

```text
1 UNION SELECT NULL
```

**Observation**

The request is blocked by the Web Application Firewall.

---

## Step 4 – Bypass the WAF

Select only:

```text
1 UNION SELECT NULL
```

In Burp Suite:

**Extensions → Hackvertor → Encode → hex_entities**

The payload is converted into XML entities before being sent.

**Observation**

The application processes the request successfully, demonstrating that the WAF filter has been bypassed.

---

## Step 5 – Determine the Number of Columns

The encoded payload confirms that the query returns **one column**.

---

## Step 6 – Extract Credentials

Since only one column is available, concatenate the username and password.

```text
1 UNION SELECT username || '~' || password FROM users
```

Encode the payload using **Hackvertor** before sending it.

**Observation**

The response returns credentials in the format:

```text
username~password
```

The administrator credentials are successfully recovered. The actual password has been replaced with `<ADMINISTRATOR_PASSWORD>` in this documentation.

---

# Payload Analysis

### XML Entity Encoding

Encodes SQL keywords so they bypass input filters.

### Hackvertor

Automatically converts payloads into encoded XML entities.

### `UNION SELECT`

Retrieves data from another database table.

### `||`

Concatenates multiple values into a single output column.

---

# Why the Attack Works

The WAF validates the encoded request, but the application decodes the XML entities before executing the SQL query.

As a result, the SQL payload reaches the database in its original form while bypassing the security filter.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Comparing encoded and unencoded payloads.
* Testing XML entity encoding.
* Looking for discrepancies between WAF behavior and application behavior.
* Verifying whether the server decodes XML entities before processing input.

---

# Common Mistakes

* Encoding the entire XML request instead of only the SQL payload.
* Forgetting to determine the number of columns first.
* Assuming WAF protection eliminates SQL Injection.
* Using the wrong concatenation operator for the target database.

---

# Real-World Scenario

Many enterprise applications rely on WAFs as an additional security layer.

If the WAF fails to normalize encoded input before inspection, attackers may bypass detection using XML entities or other encoding techniques and execute SQL Injection payloads successfully.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Normalize and decode input before applying security rules.
* Configure WAFs to inspect decoded content.
* Validate input on the server side.
* Perform regular penetration testing against WAF-protected applications.

---

# Key Takeaways

* WAFs are not a replacement for secure coding.
* Encoding techniques can bypass improperly configured filters.
* Input normalization is critical before security validation.
* Parameterized queries remain the strongest defense against SQL Injection.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
