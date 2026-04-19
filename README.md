# 🔐 Brute Force Attack Detection

## 📌 Overview

Simulated a real-world **RDP Brute Force Attack** in a controlled home lab environment using Kali Linux as the attacker and Windows 10 as the target. The attack was performed using **Hydra v9.6** and detected through **Windows Security Event Logs**.

This project demonstrates the complete attack lifecycle — from execution to detection — exactly as a SOC Analyst would investigate it in a real company environment.

---

## 🎯 Project Objectives

- Simulate a realistic brute force attack against RDP (port 3389)
- Successfully crack a weak user account password using Hydra
- Detect the attack through Windows Security Event Logs
- Document findings in a professional SOC investigation report
- Map the attack to MITRE ATT&CK framework

---

## 🖥️ Lab Environment

| Component | Details |
|---|---|
| Attacker Machine | Kali Linux 2026.1 VM |
| Attacker IP | 192.168.0.141 |
| Target Machine | Windows 10 VM |
| Target IP | 192.168.0.138 |
| Target Service | RDP (Remote Desktop Protocol) |
| Target Port | 3389 |
| Network | Bridged Adapter (Isolated Lab) |
| Attack Tool | Hydra v9.6 |
| Detection | Windows Security Event Logs |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Hydra v9.6** | Brute force attack tool |
| **Windows Event Viewer** | Log analysis and detection |
| **Netstat** | Verify RDP port status |
| **Windows CMD** | Target configuration |

---

## ⚔️ Attack Methodology

### Phase 1 — Pre-Attack Setup
- Enabled RDP on Windows 10 target machine
- Created test user account (testuser)
- Verified port 3389 is LISTENING
- Prepared username and password wordlists on Kali

### Phase 2 — Brute Force Attack (Username List)
```bash
hydra -L usernames.txt -P passwords.txt rdp://192.168.0.138 -t 4 -W 3 -v
```
- Result: Credentials identified — Windows account lockout triggered ✅

### Phase 3 — Targeted Brute Force Attack
```bash
hydra -l testuser -P passwords.txt rdp://192.168.0.138 -t 2 -W 5 -v
```
- Result: Password successfully cracked — testuser:Password123 ✅

---

## 🔍 Detection Findings

### Windows Event Logs Captured

| Event ID | Event Name | Count | Finding |
|---|---|---|---|
| **4625** | Failed Logon | 72 | Brute force attempts from Kali IP |
| **4740** | Account Lockout | 1 | Windows blocked the attack |
| **4624** | Successful Logon | 507 | Normal system activity |

### Key Evidence from Event 4625
- Failure Reason: Unknown user name or bad password
- Workstation Name: **kali** ← Attack origin confirmed!
- Source Network Address: **192.168.0.141** ← Kali IP confirmed!
- 72 failed attempts in under 2 minutes = classic brute force pattern

---

## 🎯 Indicators of Compromise (IOCs)

| Type | Value |
|---|---|
| Attacker IP | 192.168.0.141 |
| Target IP | 192.168.0.138 |
| Target Port | 3389 (RDP) |
| Cracked Username | testuser |
| Cracked Password | Password123 |
| Attack Tool | Hydra v9.6 |
| Attack Pattern | 72 failed logins in 2 minutes |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique ID | Technique Name |
|---|---|
| T1110 | Brute Force |
| T1110.001 | Password Guessing |
| T1078 | Valid Accounts |
| T1021.001 | Remote Desktop Protocol |

---

## 🚩 Red Flags Identified

- 72 failed login attempts in under 2 minutes
- All attempts from same source IP
- Workstation identified as "kali" — known pentesting OS
- Sequential password attempts matching wordlist pattern
- Account lockout triggered by Windows security policy
- RDP exposed without MFA or IP restriction

---

## 🛡️ Recommendations

1. Disable RDP if not required
2. Enforce strong password policy (min 12 characters)
3. Enable Multi-Factor Authentication for RDP
4. Implement account lockout policy (lock after 5 failed attempts)
5. Restrict RDP access to specific IPs via firewall
6. Use VPN instead of exposing RDP directly
7. Deploy SIEM to alert on brute force patterns in real time

---

## 📁 Repository Structure

```
📁 brute-force-attack-detection/
│
├── 📄 README.md
├── 📄 Brute-Force-Investigation-Report.docx
└── 📁 evidence/
    ├── 01-rdp-enabled.png
    ├── 02-testuser-created.png
    ├── 03-rdp-port-listening.png
    ├── 04-hydra-attack-start.png
    ├── 05-hydra-credentials-blocked.png
    ├── 06-hydra-password-cracked.png
    ├── 07-event4625-kali-ip.png
    └── 08-event4740-lockout.png
```

---

## 📸 Evidence Screenshots

| File | Description |
|---|---|
| 01-rdp-enabled.png | RDP enabled on Windows 10 target |
| 02-testuser-created.png | Test user account creation |
| 03-rdp-port-listening.png | Port 3389 confirmed LISTENING |
| 04-hydra-attack-start.png | Hydra attack initiated |
| 05-hydra-credentials-blocked.png | Credentials found — account locked |
| 06-hydra-password-cracked.png | Password cracked successfully |
| 07-event4625-kali-ip.png | Event 4625 showing Kali as attacker |
| 08-event4740-lockout.png | Event 4740 — account lockout |

---

## ⚠️ Disclaimer

This project was conducted in an **isolated home lab environment** for **educational purposes only**. All attacks were performed on virtual machines I personally own. No real-world systems were targeted or harmed.

---

## 👩‍💻 About

**Meenakshy B R**
Cybersecurity Enthusiast | Aspiring SOC Analyst

🔗 [GitHub Profile](https://github.com/Meenakshy10) 
