# [cite_start]Active Directory Home Lab: Exploitation & Defense [cite: 2]

## 📖 Overview
[cite_start]This project is a comprehensive, hands-on lab focused on executing a complete Red Team cyber kill chain[cite: 18]. [cite_start]The engagement covers everything from initial access to full domain compromise, followed by localized defensive exercises to engineer the detection of these specific threat vectors[cite: 18]. [cite_start]The environment mirrors a real-world Advanced Persistent Threat (APT) attack lifecycle against a provisioned Windows Server Domain Controller[cite: 19, 20].

## 🏗️ Architecture & Network Setup
* [cite_start]**Domain Name:** haouari.local[cite: 75].
* [cite_start]**Domain Controller (DC01):** A Windows Server configured with Active Directory Domain Services (AD DS)[cite: 49].
* [cite_start]**Target Workstation (WS01):** A domain-joined Windows Workstation acting as the primary entry point[cite: 60].
* [cite_start]**Attacker Machine:** A Kali Linux virtual machine simulating an internal threat actor[cite: 34].
* [cite_start]**Defensive Tooling:** Microsoft Sysmon for granular endpoint monitoring and Splunk Enterprise as the SIEM solution[cite: 101, 106].

## ⚔️ Attack Simulations (Red Team)
* [cite_start]**Initial Access:** Poisoned native Windows broadcast protocols (LLMNR and NBT-NS) to capture NTLM handshakes[cite: 21].
* [cite_start]**Identity Attacks:** Executed AS-REP Roasting and Kerberoasting to extract and crack hashes, including the `svc-sql` service account[cite: 129, 142, 143].
* [cite_start]**Lateral Movement:** Leveraged cracked credentials to establish a remote shell using Evil-WinRM[cite: 137].
* [cite_start]**Privilege Escalation:** Deployed the GodPotato exploit to abuse `SeImpersonatePrivilege` and elevate the session to `NT AUTHORITY\SYSTEM`[cite: 23].
* [cite_start]**Credential Harvesting & Persistence:** Executed Mimikatz in-memory to dump LSASS and extracted the `krbtgt` hash to forge a Golden Ticket for persistent access[cite: 24, 211, 212].

## 🛡️ Detection & Telemetry (Blue Team)
* [cite_start]**Endpoint Telemetry:** Configured Sysmon to capture Process Creation (Event ID 1) and Process Access to LSASS (Event ID 10)[cite: 218].
* [cite_start]**SIEM Integration:** Ingested Sysmon telemetry into Splunk Enterprise to build a dedicated SOC dashboard[cite: 251, 253].
* [cite_start]**Threat Hunting:** Successfully visualized the execution of privilege escalation and credential dumping tools, even with Windows Defender Real-Time Protection disabled[cite: 253].

## 🔒 Remediation & Hardening
* [cite_start]**Network Security:** Disabled LLMNR and NBT-NS, and enforced SMB Signing to prevent NTLM relay attacks[cite: 294, 311, 332].
* [cite_start]**Identity Security:** Enforced Kerberos Pre-Authentication and transitioned vulnerable service accounts to Group Managed Service Accounts (gMSA)[cite: 364, 407].
* [cite_start]**Access Control:** Audited Active Directory ACLs to remove misconfigurations, ensuring standard users do not hold Generic Write or Force Change Password permissions[cite: 417].
* [cite_start]**Endpoint Hardening:** Enabled LSA Protection (RunAsPPL), disabled WDigest, and implemented Windows Defender Credential Guard to prevent in-memory credential dumping[cite: 534, 540, 541].
* [cite_start]**Incident Response:** Reset the `krbtgt` domain account password twice to invalidate forged Kerberos tickets and lock out the persistent backdoor[cite: 551, 552].
