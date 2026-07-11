# Blind SQL Injection with Out-of-Band Data Exfiltration

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | SQL Injection                    |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* Blind SQL Injection
* Out-of-Band (OAST) SQL Injection
* Data Exfiltration
* Burp Collaborator
* Oracle Database
* Burp Suite Repeater

---

# Overview

This lab extends the Out-of-Band SQL Injection technique by using **Burp Collaborator** not only to confirm SQL execution but also to **exfiltrate sensitive data** from the database.

Instead of simply triggering an external request, the injected SQL embeds the administrator's password into the DNS request sent to Burp Collaborator, allowing sensitive information to be extracted even when the application provides no visible feedback.

---

# Lab Objective

Exploit a Blind SQL Injection vulnerability to retrieve the administrator's password through an out-of-band DNS request.

---

# Understanding the Vulnerability

Some applications provide:

* No SQL errors
* No query results
* No timing differences

In these situations, attackers can abuse database features capable of making external network requests.

By embedding sensitive data into the request itself, information can be extracted through Burp Collaborator without relying on the application's response.

---

# Attack Methodology

1. Capture the vulnerable request.
2. Send the request to Burp Repeater.
3. Replace the TrackingId cookie with the Out-of-Band data exfiltration payload.
4. Insert a Burp Collaborator payload.
5. Send the request.
6. Poll Burp Collaborator.
7. Extract the administrator password from the DNS interaction.
8. Log in using the recovered credentials.

---

# Solution Walkthrough

## Step 1 – Capture the Request

Open the lab in **Burp Browser**.

Locate the request containing the `TrackingId` cookie and send it to **Burp Repeater**.

---

## Step 2 – Replace the TrackingId Cookie

Replace the cookie with the Out-of-Band data exfiltration payload.

Locate:

```text
BURP-COLLABORATOR-SUBDOMAIN
```

Right-click and choose:

**Insert Collaborator Payload**

Burp automatically replaces it with your unique Collaborator domain.

---

## Step 3 – Send the Request

Send the modified request.

The injected SQL retrieves the administrator password and embeds it into the hostname of the DNS request sent to Burp Collaborator.

---

## Step 4 – Poll Burp Collaborator

Open:

**Burp → Collaborator**

Click:

**Poll now**

Wait a few seconds if no interaction appears immediately.

---

## Step 5 – Extract the Password

Open the received interaction.

The administrator password appears as the **first subdomain** of the DNS request.

For documentation purposes, the recovered password is represented as:

```text
<ADMINISTRATOR_PASSWORD>.<COLLABORATOR_DOMAIN>
```

Use the recovered credentials to authenticate as the administrator and complete the lab.

---

# Payload Analysis

### `SELECT password FROM users`

Retrieves the administrator password from the database.

### `||`

Concatenates the password into the generated DNS request.

### `EXTRACTVALUE(xmltype())`

Processes XML while triggering an external request.

### Burp Collaborator

Receives the DNS request containing the embedded password.

---

# Why the Attack Works

Although the application never returns query results, the database still executes the injected SQL.

By embedding the query result inside a DNS request sent to Burp Collaborator, sensitive information is exfiltrated through an external communication channel completely independent of the application's response.

---

# Detection Tips

Security testers can identify this vulnerability by:

* Testing Burp Collaborator payloads.
* Monitoring outbound DNS and HTTP interactions.
* Looking for database functions capable of external communication.
* Inspecting Collaborator requests for embedded application data.

---

# Common Mistakes

* Forgetting to replace the Collaborator placeholder.
* Polling Collaborator before sending the request.
* Looking for the password in the webpage instead of the DNS interaction.
* Using database-specific payloads on an unsupported DBMS.

---

# Real-World Scenario

In highly restricted environments where SQL errors, query results, and timing differences are unavailable, attackers can still exfiltrate sensitive information through outbound DNS or HTTP requests.

Monitoring unexpected outbound traffic from database servers is therefore an important defensive measure.

---

# Mitigation

Developers should:

* Use parameterized queries.
* Restrict outbound network access from database servers.
* Disable unnecessary XML functionality.
* Monitor DNS and HTTP traffic originating from database systems.
* Perform regular security testing for Out-of-Band SQL Injection.

---

# Key Takeaways

* Out-of-Band SQL Injection can both verify vulnerabilities and extract sensitive data.
* Burp Collaborator provides a reliable external communication channel.
* DNS requests can be abused to leak confidential information.
* Restricting outbound connectivity significantly reduces the impact of this attack.

---

# References

* PortSwigger Web Security Academy
* OWASP SQL Injection Prevention Cheat Sheet
* Burp Suite Collaborator Documentation

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
