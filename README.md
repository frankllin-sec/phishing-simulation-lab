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

```bash
# Navigate to home directory
cd ~

# Download GoPhish
wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip

# Extract
unzip gophish-v0.12.1-linux-64bit.zip -d gophish

# Enter directory and set permissions
cd gophish
chmod +x gophish

# Run GoPhish
sudo ./gophish
```

Once running, the admin panel is accessible at:
```
https://127.0.0.1:3333
```

> 📸 *[Screenshot: GoPhish running in terminal + admin panel loaded]*

---

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

> 📸 *[Screenshot: Sending Profile configured in GoPhish]*

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

> 📸 *[Screenshot: Email Template configured in GoPhish]*  
> 📸 *[Screenshot: Email received in Gmail inbox]*

---

### ✅ Phase 4 — Landing Page (Fake Google Login)

Created a fake Google login page using custom HTML/CSS.  
The page visually mimics the real Google sign-in page with the colored Google logo, email and password fields, and a "Next" button.

**Capture settings enabled:**
- ✅ Capture Submitted Data
- ✅ Capture Passwords
- 🔁 Redirect to: `https://google.com` (after form submission)

> 📸 *[Screenshot: Fake Google login page rendered in browser]*

---

### ✅ Phase 5 — Users & Groups (Target Configuration)

Created a test target group to simulate a real employee list.  
In a real authorized engagement, this would contain actual employee emails provided by the client company.

| First Name | Last Name | Email | Position |
|---|---|---|---|
| John | Smith | [test-email]@gmail.com | Employee |

> 📸 *[Screenshot: Users & Groups configured in GoPhish]*

---

### ✅ Phase 6 — Campaign Setup

Created the phishing campaign connecting all configured components.

| Field | Value |
|---|---|
| Campaign Name | Lab-Phishing-Campaign-04 |
| Email Template | Google-Security-Alert |
| Landing Page | Google-Login-Page |
| URL | http://127.0.0.1:8080 |
| Sending Profile | Lab-SMTP-Profile |
| Groups | Lab-Targets |

**⚙️ URL Configuration Note:**  
Used `http://127.0.0.1:8080` because GoPhish is running locally inside a Kali Linux VM.  
The phishing page is only accessible within the lab environment.

> **For real authorized engagements:**  
> The URL should be the IP address or domain of the server running GoPhish — accessible by employees on the internal network (e.g., `http://192.168.1.50`) or a cloud-hosted server for external simulations (e.g., a registered domain on AWS or DigitalOcean).

> 📸 *[Screenshot: Campaign setup form in GoPhish]*

---

### ✅ Phase 7 — Campaign Launch

Launched the campaign. The phishing email was delivered to the target inbox successfully.

> 📸 *[Screenshot: Campaign launched — email delivered]*

---

### 🔧 Phase 8 — Troubleshooting: Port Configuration Fix

**Problem:** After clicking the phishing link in the email, the browser displayed raw HTML code instead of rendering the page.

**Root Cause:** GoPhish's phishing server was configured to listen on **port 80**, which requires root-level privileges on Linux. The server was failing to bind to that port silently.

**Solution:** Edited the GoPhish configuration file to change the phishing server port from `80` to `8080`:

```bash
nano ~/gophish/config.json
```

Changed:
```json
"listen_url": "0.0.0.0:80"
```
To:
```json
"listen_url": "0.0.0.0:8080"
```

Restarted GoPhish and updated the campaign URL to `http://127.0.0.1:8080`.  
The page rendered correctly after the fix.

> 📸 *[Screenshot: config.json edited in nano]*  
> 📸 *[Screenshot: Page rendering correctly after fix]*

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

> 📸 *[Screenshot: GoPhish results dashboard showing all metrics]*

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

## 🔗 Related Projects

| Project | Description |
|---|---|
| [IP Investigation Lab](https://github.com/frankllin-sec) | OSINT investigation of a suspicious IP using VirusTotal, AbuseIPDB, and WHOIS |
| [Home Lab SIEM](https://github.com/frankllin-sec) | Splunk SIEM setup with Windows + Kali Linux VMs |
| [Cybersecurity Journey](https://github.com/frankllin-sec) | Full SOC Analyst learning path and progress tracker |

---

## 🗺️ Certification Roadmap

| Certification | Status |
|---|---|
| TryHackMe SOC Level 1 | 🔄 In Progress |
| CompTIA Security+ | 🔄 Studying |
| BTL1 — Blue Team Level 1 | 🔜 Planned |
| Splunk Core Certified User | 🔄 In Progress |

---

<p align="center">Made with 🛡️ by <a href="https://github.com/frankllin-sec">Frankllin</a></p>
