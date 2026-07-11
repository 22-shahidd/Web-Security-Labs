# SQL Injection

A collection of write-ups documenting my journey through the **SQL Injection** module of the **PortSwigger Web Security Academy**.

This repository contains solutions, explanations, attack methodologies, and defensive techniques for every SQL Injection lab I completed. The goal is to build a structured learning resource while strengthening my understanding of one of the most critical web application vulnerabilities.

---

## About SQL Injection

SQL Injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the SQL queries an application makes to its database.

When user input is incorporated into SQL queries without proper validation or parameterized statements, attackers may be able to:

* Bypass authentication
* Access unauthorized data
* Enumerate database structures
* Modify or delete database records
* Execute database-specific functions
* Exfiltrate sensitive information

SQL Injection remains one of the most impactful web vulnerabilities and has been featured in the OWASP Top 10 for many years.

---

# Skills Gained

Throughout these labs, I practiced:

* SQL Injection fundamentals
* Authentication bypass
* Database fingerprinting
* Database enumeration
* UNION-based SQL Injection
* Blind SQL Injection
* Error-based SQL Injection
* Time-based SQL Injection
* Out-of-Band (OAST) SQL Injection
* WAF bypass using XML encoding
* Burp Suite Repeater
* Burp Suite Intruder
* Burp Collaborator
* Payload analysis
* Secure coding concepts and mitigations

---

# Lab Progress

| Lab | Topic                                                     | Status |
| --- | --------------------------------------------------------- | ------ |
| 01  | SQL Injection in WHERE Clause                             | ✅      |
| 02  | Login Bypass                                              | ✅      |
| 03  | Database Type & Version (Oracle)                          | ✅      |
| 04  | Database Type & Version (MySQL/MSSQL)                     | ✅      |
| 05  | Database Enumeration (Non-Oracle)                         | ✅      |
| 06  | Database Enumeration (Oracle)                             | ✅      |
| 07  | UNION Attack – Determine Column Count                     | ✅      |
| 08  | UNION Attack – Find Text Column                           | ✅      |
| 09  | UNION Attack – Retrieve Data                              | ✅      |
| 10  | UNION Attack – Multiple Values in One Column              | ✅      |
| 11  | Blind SQL Injection – Conditional Responses               | ✅      |
| 12  | Blind SQL Injection – Conditional Errors                  | ✅      |
| 13  | Visible Error-Based SQL Injection                         | ✅      |
| 14  | Blind SQL Injection – Time Delays                         | ✅      |
| 15  | Blind SQL Injection – Time Delays & Information Retrieval | ✅      |
| 16  | Blind SQL Injection – Out-of-Band Interaction             | ✅      |
| 17  | Blind SQL Injection – Out-of-Band Data Exfiltration       | ✅      |
| 18  | SQL Injection with Filter Bypass via XML Encoding         | ✅      |

---

# Tools Used

* Burp Suite Community Edition
* Burp Repeater
* Burp Intruder
* Burp Collaborator
* Hackvertor
* PortSwigger Web Security Academy

---

# Key Concepts Learned

## Basic SQL Injection

Understanding how user input can alter SQL query logic.

## UNION-Based SQL Injection

Combining attacker-controlled queries with legitimate application queries to retrieve unauthorized data.

## Database Enumeration

Discovering database tables, columns, and schema information before extracting sensitive data.

## Blind SQL Injection

Extracting information through:

* Conditional responses
* Conditional errors
* Time delays

## Out-of-Band SQL Injection (OAST)

Using Burp Collaborator to verify SQL Injection and exfiltrate data through external DNS/HTTP interactions.

## WAF Bypass

Using XML entity encoding to bypass input filters and execute SQL payloads.

---

# Defensive Measures

To prevent SQL Injection:

* Use parameterized queries (prepared statements)
* Avoid dynamic SQL query construction
* Validate and sanitize user input
* Apply the principle of least privilege
* Disable verbose database error messages
* Normalize input before security inspection
* Perform regular penetration testing

---

# Repository Structure

```text
SQL-Injection/
│
├── README.md
├── Lab-01/
├── Lab-02/
├── ...
└── Lab-18/
```

---

# Learning Outcome

Completing these labs strengthened my understanding of:

* SQL query behavior
* Secure database interaction
* Web application security testing
* Manual exploitation techniques
* Burp Suite workflows
* OWASP-recommended defensive practices

---

# Related Resources

* 🌐 Medium Article: **Coming Soon**
* 💻 GitHub Repository: *(Root repository)*
* 📚 Platform: PortSwigger Web Security Academy

---

> **Disclaimer:** These write-ups document labs completed in the PortSwigger Web Security Academy. They are intended solely for educational purposes within authorized environments.
