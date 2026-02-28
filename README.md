# Active Directory Home Lab: Exploitation & Defense

## 📖 Overview
This project is a comprehensive, hands-on lab focused on executing a complete Red Team cyber kill chain. The engagement covers everything from initial access to full domain compromise, followed by localized defensive exercises to engineer the detection of these specific threat vectors. The environment mirrors a real-world Advanced Persistent Threat (APT) attack lifecycle against a provisioned Windows Server Domain Controller (`haouari.local`).

## 🏗️ Architecture & Network Setup
* **Domain Controller (DC01):** Windows Server running AD DS at `192.168.78.10`.
* **Target Workstation (WS01):** Domain-joined Windows endpoint at `192.168.78.20`.
* **Attacker Machine:** Kali Linux virtual machine at `192.168.78.133`.

![Kali Linux Network Configuration](images/kali-ifconfig.png)

* **Defensive Tooling:** Microsoft Sysmon deployed for granular endpoint monitoring and Splunk Enterprise configured as the central SIEM.

![Splunk Enterprise Dashboard](images/splunk-home.png)

## ⚔️ Attack Simulations (Red Team)

### 1. Initial Access & Identity Attacks
The attack commenced by poisoning native Windows broadcast protocols (LLMNR and NBT-NS) to capture NTLM handshakes. Following this, AS-REP Roasting and Kerberoasting were executed to extract and crack hashes, including the `svc-sql` service account. 

![AS-REP Roasting with GetNPUsers](images/asrep-roasting.png)

### 2. Lateral Movement & Defense Evasion
Using cracked credentials, a foothold was established via Evil-WinRM. BloodHound enumeration revealed that the compromised user had the rights to force a password change for the highly privileged `it-support` account. Windows Defender Real-Time Protection was deliberately disabled via the command line to create a blind spot.

![BloodHound Attack Path Analysis](images/bloodhound-path.png)

### 3. Privilege Escalation & Credential Harvesting
The GodPotato (`gp.exe`) exploit was deployed to abuse `SeImpersonatePrivilege`, elevating the session to `NT AUTHORITY\SYSTEM`. From this context, Mimikatz was executed to dump LSASS memory and extract the Domain Administrator hashes.

![Mimikatz LSASS Memory Dump](images/mimikatz-execution.png)

## 🛡️ Detection & Telemetry (Blue Team)
Despite defense evasion tactics, the custom Sysmon deployment successfully logged the in-memory credential dumping under Event ID 1 (Process Creation) and Event ID 10 (Process Access to LSASS). 

![Sysmon Event ID 1 - Mimikatz Execution](images/sysmon-event1.png)

This telemetry was ingested into Splunk, allowing for the engineering of a dedicated SOC dashboard to instantly visualize the execution of the privilege escalation tools.

![Splunk Threat Hunting Dashboard](images/splunk-search-mimikatz.png)

## 🔒 Remediation & Hardening
To secure the `haouari.local` environment against these advanced attack paths, several structural hardening measures were implemented:

* **Network Security:** Disabled LLMNR via Group Policy and enforced SMB Signing to neutralize NTLM relay attacks.
![Disable LLMNR GPO](images/disable-llmnr.png)

* **Identity & Access Security:** Enforced Kerberos Pre-Authentication to defeat AS-REP Roasting, and transitioned the vulnerable `svc-sql` account to a Group Managed Service Account (gMSA).
* **Endpoint Hardening:** Enabled LSA Protection (RunAsPPL) and implemented Windows Defender Credential Guard to prevent Mimikatz from extracting plaintext passwords.
* **Incident Response:** The `krbtgt` domain account password was reset twice to invalidate any forged Golden Tickets and lock out persistent backdoors.

![Resetting krbtgt Password](images/krbtgt-reset.png)

---

## 📄 Full Technical Report
For a comprehensive, step-by-step technical breakdown of the methodology, command execution, and remediation strategies, please refer to the full project report included in this repository.

[**View the Full Project Report (PDF)**](Project_Report_AD.pdf)
