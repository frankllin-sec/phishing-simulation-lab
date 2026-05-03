# 🎣 Phishing Simulation Lab — GoPhish

<p align="center">
  <img src="https://img.shields.io/badge/Tool-GoPhish-orange?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Platform-Kali%20Linux-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Type-Phishing%20Simulation-red?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Role-SOC%20Analyst-purple?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Completed-green?style=for-the-badge"/>
</p>

---

## ⚠️ Disclaimer

> This project was conducted in a **fully controlled lab environment** for **educational purposes only**.  
> No real users were targeted. No real credentials were harvested outside of a test account owned by the author.  
> Performing phishing simulations without **explicit written authorization** is illegal.  
> This lab simulates what a Security Analyst would do in an **authorized** corporate engagement.

---

## 📌 About This Project

This project demonstrates a complete **phishing simulation lifecycle** using **GoPhish** inside a Kali Linux VM.  
The goal is to simulate how attackers craft phishing emails and credential harvesting pages.

The simulation covers:
- Setting up a phishing infrastructure from scratch
- Crafting a convincing phishing email and fake login page
- Launching a campaign and tracking victim interaction
- Analyzing the captured data
- Understanding how to build a security awareness response

---
## 📋 Step-by-Step Walkthrough

---

### ✅ Phase 1 — GoPhish Installation on Kali Linux

Installed GoPhish inside a Kali Linux VM running on VirtualBox.


Once running, the admin panel is accessible at:
```
https://127.0.0.1:3333
```


📸<a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/Installed-pt-1.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/Installed-pt-1.png" width="400"/>
</a>


### ✅ Phase 2 — Sending Profile (SMTP Configuration)

Configured the email delivery profile using Gmail's SMTP server.  
A dedicated **App Password** was generated from Google Account settings — this is required because Google blocks third-party apps from using the main account password directly.

| Field | Value |
|---|---|
| Name | Lab-SMTP-Profile |
| Interface Type | SMTP |
| SMTP From | security@company-lab.com |
| Host | smtp.gmail.com:587 |
| Username | [test-email]@gmail.com |
| Password | [App Password - 16 chars] |
| Ignore Certificate Errors | ✅ Enabled |

> 📸 <a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/Sending%20Profile1.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/Sending%20Profile1.png" width="300"/>
</a>

---

### ✅ Phase 3 — Email Template (Phishing Email)

Created a realistic phishing email impersonating a **Google Security Alert**.  
The email uses HTML formatting with a blue header, clear call-to-action button, and a fake device/location detail to create urgency.

**Key GoPhish variables used:**
- `{{.FirstName}}` — personalizes the email with the target's first name
- `{{.URL}}` — dynamically inserts the phishing link tied to the campaign

**Subject line:**
```
[Action Required] Unusual sign-in activity detected
```
> 📸 <a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/Template%20configured.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/Template%20configured.png" width="300"/>
</a>
---
### ✅ Phase 4 — Landing Page (Fake Google Login)

Created a fake Google login page using custom HTML/CSS.  
The page visually mimics the real Google sign-in page with the colored Google logo, email and password fields, and a "Next" button.

**Capture settings enabled:**
- ✅ Capture Submitted Data
- ✅ Capture Passwords
- 🔁 Redirect to: `https://google.com` (after form submission)

> 📸 <a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/landing-page-working-100%25.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/landing-page-working-100%25.png" width="300"/>
</a>
---
### ✅ Phase 5 — Users & Groups (Target Configuration)

Created a test target group to simulate a real employee list.  
In a real authorized engagement, this would contain actual employee emails provided by the client company.

| First Name | Last Name | Email | Position |
|---|---|---|---|
| John | Smith | [test-email]@gmail.com | Employee |

> 📸 <a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/group-target.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/group-target.png" width="300"/>
</a>
---
### ✅ Phase 6 — Campaign Setup

Created the phishing campaign connecting all configured components.

| Field | Value |
|---|---|
| Campaign Name | Lab-Phishing-Campaign-01 |
| Email Template | Google-Security-Alert |
| Landing Page | Google-Login-Page |
| URL | http://127.0.0.1:8080 |
| Sending Profile | Lab-SMTP-Profile |
| Groups | Lab-Targets |

**⚙️ URL Configuration Note:**  
Used `http://127.0.0.1:8080` because GoPhish is running locally inside a Kali Linux VM.  
The phishing page is only accessible within the lab environment.

> **For real authorized engagements:**  
> The URL should be the IP address or domain of the server running GoPhish — accessible by employees on the internal network (e.g., `http://192.168.1.50`) or a cloud-hosted server for external simulations.

📸<a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/launch-campaing.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/launch-campaing.png" width="300"/>
</a> 
### ✅ Phase 7 — Campaign Launch

Launched the campaign. The phishing email was delivered to the target inbox successfully.

> 📸 E-mail received.
> <a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/email-received_gm-ph.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/email-received_gm-ph.png" width="300"/>
</a>

---

### 🔧 Phase 8 — Troubleshooting: Port Configuration Fix

**Problem:** After clicking the phishing link in the email, the browser displayed raw HTML code instead of rendering the page.

**Root Cause:** GoPhish's phishing server was configured to listen on **port 80**, which requires root-level privileges on Linux. The server was failing to bind to that port silently.

**How I found the fix:** The page was rendering raw HTML instead of the actual login page. I researched the issue and found that on Linux systems, ports below 1024 
require root privileges to bind. Since GoPhish was not running with full root permissions on the phishing server process, port 80 was silently failing. 
The solution was to move the phishing server to port **8080**, which does not require elevated privileges.

**Solution:** Edited the GoPhish configuration file.

Restarted GoPhish and updated the campaign URL to `http://127.0.0.1:8080`.  
The page rendered correctly after the fix.

📸 **Before** — Raw HTML displayed instead of the page:

<a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/page_error-wrong-port.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/page_error-wrong-port.png" width="300"/>
</a>

📸 **After** — Page rendering correctly after changing port to 8080:

<a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/landing-page-working-100%25.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/landing-page-working-100%25.png" width="300"/>
</a>

---
### ✅ Phase 9 — Results Analysis

After clicking the link and submitting credentials on the fake login page, GoPhish captured the interaction and displayed the full results dashboard.

| Metric | Result |
|---|---|
| 📧 Email Sent | 1 |
| 👁️ Email Opened | 1 |
| 🖱️ Clicked Link | 1 |
| 🔑 Submitted Data | 1 |
| 📣 Email Reported | 0 |

**Key Finding:**  
The target clicked the link and submitted credentials — 100% of the simulated employees were successfully phished.  
The submitted email and password were captured in the GoPhish database.

> 📸 <a href="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/results-campaign-phising.png">
  <img src="https://raw.githubusercontent.com/frankllin-sec/phishing-simulation-lab/main/results-campaign-phising.png" width="300"/>
</a>

---

### ✅ Phase 10 — Security Awareness Response

Following a real phishing simulation, the next step is to launch a **security awareness campaign** targeting employees who clicked the link.

**Response actions:**
- Notify employees who clicked that they were part of a phishing simulation
- Share educational material explaining how to identify phishing emails
- Provide a checklist of red flags to look for
- Schedule a follow-up simulation 30–60 days later to measure improvement

**Key indicators employees should recognize:**
- Urgency language ("Act immediately", "Your account will be suspended")
- Mismatched sender domains
- Generic greetings instead of personalized names
- Suspicious links that don't match the real domain

---

## 🧠 Skills Demonstrated

| Skill | Application |
|---|---|
| Phishing Infrastructure Setup | Configured GoPhish end-to-end on Kali Linux |
| SMTP Configuration | Set up Gmail App Password for email delivery |
| Social Engineering | Designed convincing email and landing page |
| HTML/CSS | Built fake Google login page from scratch |
| Linux Troubleshooting | Diagnosed and fixed port binding issue via config.json |
| Campaign Management | Launched, monitored, and analyzed phishing campaign |
| Data Analysis | Interpreted GoPhish metrics dashboard |
| Security Awareness | Understands post-simulation response process |

---

## 🤖 AI-Assisted Learning

This project was built with the support of **Claude AI (Anthropic)** as a learning assistant.

AI was used to:
- Guide the step-by-step configuration of GoPhish
- Help troubleshoot technical issues during the lab
- Assist with HTML/CSS for the email template and landing page
- Review and improve documentation structure

> All hands-on work, decisions, and troubleshooting were performed directly by me 
> inside the lab environment. AI served as a mentor  not as a replacement for 
> practical execution.

*"Using AI to accelerate learning is a skill in itself  just like a SOC Analyst 
uses tools to work smarter."*

## 🔗 Related Projects

| Project | Description |
|---|---|
| [IP Investigation Lab](https://github.com/frankllin-sec) | OSINT investigation of a suspicious IP using VirusTotal, AbuseIPDB, and WHOIS |
| [Home Lab SIEM](https://github.com/frankllin-sec) | Splunk SIEM setup with Windows + Kali Linux VMs |
| [Cybersecurity Journey](https://github.com/frankllin-sec) | Full SOC Analyst learning path and progress tracker |

---



<p align="center">Made with 🛡️ by <a href="https://github.com/frankllin-sec">Frankllin</a></p>
