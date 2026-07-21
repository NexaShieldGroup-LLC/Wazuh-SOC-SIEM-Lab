# NexaShield Group LLC — Cyber Security Research & Deployment Lab
## Threat Emulation Case Study: Automated Brute-Force Detection via Centralized SIEM

## Summary
This project serves as a validated deployment blueprint engineered by **NexaShield Group LLC** to demonstrate enterprise-grade real-time security monitoring, log aggregation, and threat detection capabilities. 

Attack Simulated

Hydra RDP brute‑force  
Target: 192.168.56.4:3389  
User: Designpro7ai@outlook.com  
Wordlist: rockyou.txt

Hydra output ():

    [DATA] attacking rdp://192.168.56.4:3389/  
    [ERROR] all children were disabled due too many connection errors  
    0 valid password found

Even though Hydra failed to authenticate, it generated hundreds of failed logons, which is exactly what a SOC analyst expects
Detection Pipeline (Wazuh SIEM/XDR)

the screenshots show Wazuh detecting:
✔ Rule 60122 — Logon Failure (Unknown user or bad password)

Triggered repeatedly during Hydra’s attack.
✔ Rule 60204 — Multiple Windows Logon Failures (Severity 10)

This is the high‑severity brute‑force correlation rule.
✔ Rule 60115 — User Account Locked Out

Hydra triggered Windows lockout policy due to excessive failed attempts.
✔ Rule 92217 — Executable Dropped in Windows Root Folder

This came from PowerShell file‑creation test.
✔ Rule 61640 — Summary Event of Report Signatures

Shows Wazuh aggregating multiple authentication failures.

These detections prove your SIEM is:

    Collecting Windows Security logs

    Parsing Sysmon telemetry

    Correlating brute‑force patterns

    Alerting on high‑severity authentication abuse

To validate our defensive security stack, NexaShield engineers manually provisioned a custom, minimal adversarial environment to execute a targeted automated credential-stuffing (brute-force) attack via Hydra. This case study details how our centralized **Wazuh SIEM/XDR** architecture successfully aggregates endpoint telemetry, parses raw authentication logs, and triggers immediate out-of-the-box high-priority alert clusters to mitigate active network intrusion attempts.

---

##  Enterprise Lab Architecture

Architecture Diagram

Attacker (Ubuntu) → Windows Endpoint → Wazuh Agent → Wazuh Manager → Threat Hunting Dashboard

    Attacker VM:

        NAT + Host‑Only network

        Hydra brute‑force engine

        No Wazuh agent 

    Windows VM:

        RDP enabled

        Sysmon installed

        Wazuh agent active

        IP: 192.168.56.4

    Wazuh Manager:

        Receives logs

        Correlates events

        Displays alerts in Threat Hunting
This architecture mirrors our lean deployment model for small-to-medium businesses (SMBs) looking for cost-effective, high-visibility SIEM solutions:

*   **SIEM Manager Engine:** Wazuh Server (Centralized Log Aggregator & Alert Indexer)
*   **Client Protected Endpoint:** Standard Target Server running the NexaShield-configured Wazuh Agent
*   **Controlled Threat Actor Node:** Purpose-built, lightweight attack machine with Hydra manually compiled for a minimized resource footprint.

[ Attacker Node (Manual Hydra Setup) ]  ---> (Brute-Force Traffic) --->  [ Client Target Endpoint (Wazuh Agent) ]│(Encrypted Log Telemetry)▼[ Centralized Wazuh Dashboard ]

## 🛠️ Phase 1: Defensive Setup & Configuration
1. **Agent Deployment:** Installed, registered, and verified the Wazuh endpoint agent on the client server asset.
2. **Log Ingestion Pipeline:** Verified endpoint log forwarding configuration (`ossec.conf`) to ensure authentication logs (`/var/log/auth.log` or secure event logs) were actively collected.
3. **Telemetry Validation:** Confirmed continuous, secure cryptographic connectivity between the asset and the central management node.

---

## ⚔️ Phase 2: Attack Simulation (Hydra)
To keep testing operations realistic, our team bypassed pre-built security distributions. We manually provisioned a custom threat node, stood up an independent target VM, and initiated a high-velocity automated brute-force attack using **Hydra** against the authentication service.

**Attack Execution Format:**
```bash
hydra -l [target_user] -P [wordlist.txt] [service]://<TARGET_IP>
```
*The attack attempted rapid, repeated authentication failures to simulate an automated credential-stuffing threat actor.*

---

##  Phase 3: Detection & Threat Hunting Analysis
Wazuh’s native rules engine immediately identified the malicious behavior without requiring custom tuning, demonstrating powerful out-of-the-box enterprise protection.

###  Evidence of Detection
Below is the live forensic telemetry captured during the active triage process:

#### 1. Failed Password Attempts Detected
*This screenshot shows individual failed login events registering in the Wazuh dashboard as traffic spiked.*
![Failed Password Alerts](./docs/failed_logins.png) 

#### 2. Brute-Force Cluster Triggered
*This screenshot captures the specific high-severity alert indicating that an automated brute-force attack was underway.*
![Brute Force Triggered](./docs/brute_force_alert.png)

###  Rule Metrics Validated
*   **Failed Authentication Events:** Detected individual attempts to login using failed credentials or non-existent users.
*   **Brute-Force Cluster Alarms:** Successfully escalated the incident to a high-priority alert due to the rapid frequency of automated attempts hitting the endpoint.

---

## 🛡️ Strategic Hardening & Next Steps
To mitigate the security gaps exposed by this simulation, a production enterprise environment should implement:
1. **Wazuh Active Response:** Configure the central manager to automatically trigger a firewall drop rule on the attacker's IP address upon reaching a threshold of failed attempts.
2. **Network Defenses:** Implement strict account lockout policies, Fail2ban, or shift to mandatory multi-factor authentication (MFA).

---
# Troubleshooting Journey — Fixing Wazuh & Filebeat

Before the clean logs appeared, the Wazuh stack was broken. Filebeat was failing,
the agent was disconnected, and Sysmon logs were not being ingested.

## Problems Faced
- Filebeat failing with certificate errors
- Wazuh manager rejecting agent connections
- Old certificates causing mismatches
- Incorrect permissions on key files
- Filebeat output failing to reach OpenSearch
- Agent showing “disconnected”
- Dashboard showing no logs
- Sysmon logs not appearing
- RDP logs not ingested

## What I Did
- Removed all old certificates
- Regenerated new certs using Wazuh’s CA
- Reconfigured Filebeat YAML
- Fixed permissions (chmod 600)
- Restarted Filebeat, Manager, Indexer, Dashboard
- Verified connectivity with `filebeat test output`
- Verified agent with `agent_control -l`
- Reinstalled Wazuh agent cleanly
- Reinstalled Filebeat cleanly
- Rebooted the entire stack
- Verified logs flowing into OpenSearch
- Confirmed green status on dashboard

## Result
A fully functional SIEM pipeline capable of detecting:
- brute‑force attacks
- PowerShell abuse
- file creation
- Sysmon events
- Windows authentication failures

- 
# NexaShield Group LLC — Cybersecurity Services

## SOC Build‑Out & Monitoring
- SIEM deployment (Wazuh, Elastic, Splunk)
- Endpoint monitoring (Windows, Linux)
- Sysmon configuration
- Threat hunting dashboards
- Alert tuning & correlation rules

## Threat Emulation
- Hydra brute‑force simulation
- RDP attack surface testing
- Malware drop simulation
- PowerShell abuse detection
- Credential‑stuffing simulation

## Incident Response
- Log triage
- Attack reconstruction
- Forensic analysis
- Endpoint containment

## State & Municipal Contracts
- Compliance (NIST, CJIS, NYS Cybersecurity)
- Endpoint monitoring for agencies
- SIEM deployment for small departments
- Threat emulation for readiness testing

## Telecom SOC Augmentation
- SOC analyst support
- Log ingestion engineering
- Endpoint hardening
- Detection rule tuning
- Threat hunting

## Small Business Cybersecurity
- SIEM deployment
- Endpoint protection
- Network monitoring
- Threat detection


*lead Engineer: Alan (Alancyber- NexashieldGroupLLC)  
