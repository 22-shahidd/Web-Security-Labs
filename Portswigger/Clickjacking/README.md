# Clickjacking

This directory contains my documentation for the **Clickjacking** labs from the **PortSwigger Web Security Academy**.

Rather than simply recording lab solutions, these write-ups focus on understanding the vulnerability, analyzing why the attack succeeds, reviewing the exploit methodology, and discussing practical mitigation techniques.

---

# What is Clickjacking?

Clickjacking, also known as **UI Redressing**, is a web attack where a legitimate webpage is embedded inside an attacker-controlled website using an **iframe**.

By making the iframe transparent or positioning it beneath deceptive content, an attacker can trick a user into clicking buttons or links they never intended to interact with.

Since the interaction occurs on the legitimate website, the browser automatically includes the user's authenticated session and any required security tokens, making the request appear valid.

---

# Attack Flow

```text
Victim visits attacker-controlled page
            │
            ▼
Hidden iframe loads the legitimate application
            │
            ▼
Victim clicks a visible decoy element
            │
            ▼
Click is forwarded to the hidden application
            │
            ▼
Sensitive action is performed
```

---

# Labs Covered

| Lab                                                                                                                                            | Difficulty   |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| [Basic Clickjacking with CSRF Token Protection](./Lab-01-Basic-Clickjacking-with-CSRF-Token-Protection/)                                       | Apprentice   |
| [Clickjacking with Form Input Data Prefilled from a URL Parameter](./Lab-02-Clickjacking-with-form-input-data-prefilled-from-a-URL-parameter/) | Apprentice   |
| [Clickjacking with a Frame Buster Script](./Lab-03-Clickjacking-with-a-frame-buster-script/)                                                   | Apprentice   |
| [Exploiting Clickjacking Vulnerability to Trigger DOM-based XSS](./Lab-04-Exploiting-clickjacking-vulnerability-to-trigger-DOM-based-XSS/)     | Practitioner |
| [Multistep Clickjacking](./Lab-05-Multistep-clickjacking/)                                                                                     | Practitioner |

---

# Key Concepts Learned

Throughout these labs, I explored several important clickjacking concepts:

* Basic clickjacking attacks
* Clickjacking despite CSRF protection
* Exploiting URL-prefilled form inputs
* Bypassing JavaScript frame-buster protections
* Chaining Clickjacking with DOM-based XSS
* Multi-step clickjacking attacks
* Browser security mechanisms
* Defensive HTTP security headers

---

# Mitigation Techniques

Common defenses against clickjacking include:

* **X-Frame-Options**

  * `DENY`
  * `SAMEORIGIN`

* **Content Security Policy**

  * `frame-ancestors`

* Additional confirmation for sensitive actions

* Re-authentication for high-risk operations

* Careful security review of user workflows

---

# Skills Demonstrated

* Web Application Security
* Clickjacking
* Browser Security
* HTML & CSS
* UI Redressing
* Burp Suite
* Security Testing
* Vulnerability Analysis

---

# References

* PortSwigger Web Security Academy
* OWASP Clickjacking Defense Cheat Sheet
* OWASP Web Security Testing Guide

---

> **Note:** These write-ups document labs completed in the PortSwigger Web Security Academy. The explanations are written in my own words to reinforce my understanding of web application security and serve as a personal learning resource.
