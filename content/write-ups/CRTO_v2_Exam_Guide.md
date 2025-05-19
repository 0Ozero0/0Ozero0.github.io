---
title: "CRTO v2 Exam Guide and Tips"
date: 2025-05-19
tags: [
  "CRTO", "Red Team", "CRTO v2", "Red Team Ops I", "CRTO Exam", "CRTO Tips", 
  "Red Teaming", "Offensive Security", "Zero-Point Security", "CRTP", "CRTE", 
  "AD Attack", "Kerberos", "Active Directory", "Persistence", "Lateral Movement", 
  "Credential Theft", "Beacon", "Cobalt Strike", "CS", "Command and Control", 
  "C2", "AMSIBypass", "Defender Evasion", "Shadow Credentials", "RBCD", 
  "NTLM Relay", "Domain Trust", "One Way Trust", "DPAPI", "Webshell", "Red Team Lab", 
  "Red Team Certification", "Lab Notes", "CRTO Notes", "Cobalt Strike Notes", 
  "MalOp", "OPSEC", "Evasion", "LAPS", "MSSQL", "Privilege Escalation", 
  "Windows Post Exploitation", "Pivoting", "Session Passing", "CRTO Guide", 
  "Penetration Testing", "APT Simulation", "Red Team Workflow", "Cybersecurity", 
  "Adversary Simulation", "TTPs", "Tactics Techniques and Procedures", 
  "MITRE ATT&CK", "Realistic Red Teaming", "Hacking Certifications"
]
author: "@zero"
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: true
description: "A comprehensive student-written guide to passing the CRTO v2 (Red Team Ops I) exam, including tips, mistakes to avoid, and comparisons with CRTP and CRTE."
canonicalURL: "https://canonical.url/to/page/crto-v2-guide"
disableHLJS: false
disableShare: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "https://import.cdn.thinkific.com/584845/courses/1646245/RnGN51i8QZC0FoUMH8nJ_rto_course_card.png"
    alt: "CRTO v2 Exam Guide"
    caption: "CRTO v2 - Red Team Ops I"
    relative: false
    hidden: false
---



# CRTO v2 Exam Guide and Overview

Hi everyone,

This blog is not study material, but a guide for those who want to pass the **Red Team OPS I exam (CRTO v2)**.

---

## What is CRTO?

The **CRTO course** focuses on **Active Directory misconfigurations**, but unlike CRTP or CRTE, you must perform all tasks through a **C2 server** instead of the traditional methods.

> (I'll post the differences between CRTO, CRTE, and CRTP at the end of this blog.)

You are provided with a valid license key. The course covers the following topics:

- MISC
- Command & Control
- Setup CS Listeners
- Defender Antivirus / AMSI
- Initial Compromise
- Host Reconnaissance
- Host Persistence
- Host Privilege Escalation
- Credential Theft
- Domain Recon
- User Impersonation
- Lateral Movement
- Session Passing
- Pivoting
- Data Protection API (DPAPI)
- Kerberos
- Active Directory Certificate Services
- Group Policy
- MSSQL Servers
- Domain Dominance
- Forest & Domain Trusts
- LAPS
- AppLocker
- Data Exfiltration

---

## CRTO Lab

The lab is charged by runtime. You purchase time blocks like:

- 20 hours (valid for 15 days)
- 40 hours (valid for 30 days)

**Important:** Don't make the mistake I did — I bought lab time and left it running while sleeping 😅

Lab access is via the CyberRanges dashboard — **no VPN needed**.

You’re given a **Windows Server 2022 (Attacker Desktop)** machine, and other machines will be used as you go through the course.

---

## Exam Guide – How to Pass the CRTO Exam

- Study all the course content. **Don’t skip any modules**.
- Practice in the lab with **Windows Defender enabled**, because the exam machines have it enabled. Make sure you test:
  - `amsi_disable`
  - `execute-assembly`
  - `powerpick`

- Prepare your **C2 profile** properly with the settings taught in the course.

- Don’t skip topics like **LOLBAS**, it’s very important during lateral movement for maintaining stable shells.

- `teamserver` in `/opt/cobaltstrike` is already running via Docker. If you update your C2 profile, **restart it**. Use `sudo -l` to view available options.

- Be sure your **Artifact Kit**, **Resource Kit**, and **Mimikatz Kit** are properly set up — I used all of them in the exam.

- AMSI/Defender bypass techniques taught in the course **will be needed in the exam**.

- The AMSI script provided in the course didn’t work for me during the exam, so I searched online for a working version. **Test everything in the lab first**.

- The **Pivoting with Windows** and **Pivoting with MSSQL** sections are **very important**.

- Tips from RastMouse:
  - **No password/hash cracking** is needed in the exam.
  - Attacks like **Kerberoasting**, **AS-REP Roasting**, etc., are **not required**.

- If you face an issue during the exam, search in the **Discord server** — someone might have already posted a solution.

- Don’t overthink it — **everything is from the course**.

> 🛠️ **Use only the tools provided in the exam environment — that’s all you’ll need.**

- Exam Infrastructure Setup check >> [https://github.com/An0nUD4Y/CRTO-Notes/blob/main/CRTO%20Checklist/Exam%20Infrastructure%20setup.md]


---

## 🔁 Bonus: Difference Between CRTP, CRTE, and CRTO

- **CRTP**: Great for beginners. Covers **basic to intermediate AD attacks**.
- **CRTE**: A step up from CRTP. Introduces **more complex Active Directory misconfigurations**.
- **CRTO**: Tailored for those interested in **Red Team operations and APT simulation**, not just pentesting.

### FAQ:

- **New to AD pentesting?** → Start with CRTP.
- **Can I take CRTE without CRTP?** → Yes! Most CRTP topics are covered in CRTE — just **study and practice well**.
- **Interested in APT simulation and real Red Team operations?** → CRTO is a perfect choice.

---

Good luck with your CRTO journey! 🚩


### 📱 Find me on social media:

- [LinkedIn](https://www.linkedin.com/in/ahmed-muhammed-709429242/)
- [X (Twitter)](https://x.com/AhmedMoham37657)