# Atomic Red Team Persistence Cleanup (Windows)

## 🔐 Overview
This project documents the investigation and remediation of **unintended Atomic Red Team persistence artifacts** discovered on a personal Windows laptop. The activity mimicked real-world attacker behavior and resulted in multiple administrative tools launching automatically at startup/logon.

The objective of this project was to:
- Identify the root cause of abnormal startup behavior
- Map observed activity to **MITRE ATT&CK techniques**
- Fully sanitize the system
- Document the cleanup using an incident-response mindset

This repository demonstrates **blue team skills** in detection, analysis, and remediation of persistence mechanisms.

---

## 🧠 Scenario Summary

**Observed behavior:**
- Notepad opening automatically (multiple instances)
- Event Viewer and Computer Management launching without user interaction
- Behavior triggered after reboot and when connecting AC power

**Initial concern:**
- Possible malware persistence or unauthorized scheduled execution

**Outcome:**
- Activity confirmed as **Atomic Red Team adversary emulation artifacts** left behind after lab usage
- No malicious payloads identified
- Full cleanup performed

---

## 🔎 Investigation Process

### 1. Detection
- User-observed abnormal startup behavior
- Correlated execution timing with logon/startup events

### 2. Analysis
- Reviewed **Windows Task Scheduler**
- Identified multiple suspicious tasks authored by `AtomicRedTeam`
- Tasks executed common binaries (`notepad.exe`, `calc.exe`, `eventvwr.msc`, `compmgmt.msc`)

### 3. Root Cause
- Atomic Red Team tests for **MITRE ATT&CK T1053.005 – Scheduled Task / Job** were executed previously
- Cleanup was not performed, leaving persistence mechanisms active on a personal system

---

## 🧩 MITRE ATT&CK Mapping

| ATT&CK ID | Technique | Tactic |
|----------|----------|--------|
| T1053.005 | Scheduled Task / Job: Scheduled Task | Persistence, Privilege Escalation |
| T1059.001 | Command and Scripting Interpreter: PowerShell | Execution |
| T1059.003 | Windows Command Shell | Execution |
| T1218 | System Binary Proxy Execution | Defense Evasion |
| T1036.005 | Masquerading: Legitimate Name or Location | Defense Evasion |
| T1112 | Modify Registry | Defense Evasion |

---

## 🧹 Remediation Steps

### Scheduled Tasks
- Deleted all tasks containing:
  - `Atomic`
  - `ATOMIC`
  - `T1053`
  - `Bypass`

### Registry Cleanup
Executed as Administrator:

```powershell
Remove-Item -Recurse -Force HKCU:\SOFTWARE\ATOMIC* -ErrorAction SilentlyContinue
Remove-Item -Recurse -Force HKLM:\SOFTWARE\ATOMIC* -ErrorAction SilentlyContinue
```

### Persistence Review
- Verified startup folders were clean
- Reviewed `Run` registry keys
- Checked Windows services for anomalies

---

## ✅ Validation

- `sfc /scannow`
- `DISM /Online /Cleanup-Image /RestoreHealth`
- Microsoft Defender **Offline Scan**

**Result:** No remaining persistence or malicious artifacts detected.

---

## 📌 Lessons Learned

- Adversary emulation tools must only be used in **isolated lab environments (VMs)**
- Scheduled Tasks are a high-risk persistence vector
- Post-lab cleanup is critical
- Routine audits of startup mechanisms can uncover stealthy persistence

---

## 🧠 Skills Demonstrated

- Windows persistence analysis
- Scheduled Task abuse detection
- MITRE ATT&CK mapping
- Incident response documentation
- System sanitization and validation

---

## 🚀 Future Improvements

- Reproduce the technique safely in a VM
- Add Defender / Sysmon detection mapping
- Expand into a full Atomic Red Team vs Defender lab

---

## 📫 About Me

Aspiring SOC / Blue Team analyst with hands-on experience investigating persistence mechanisms, adversary emulation artifacts, and Windows internals.

📌 *This repository is part of my cybersecurity portfolio.*

