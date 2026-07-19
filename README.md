# NexaShield Group LLC — Cyber Security Research & Deployment Lab
## Threat Emulation Case Study: Automated Brute-Force Detection via Centralized SIEM

## Summary
This project serves as a validated deployment blueprint engineered by **NexaShield Group LLC** to demonstrate enterprise-grade real-time security monitoring, log aggregation, and threat detection capabilities. 

To validate our defensive security stack, NexaShield engineers manually provisioned a custom, minimal adversarial environment to execute a targeted automated credential-stuffing (brute-force) attack via Hydra. This case study details how our centralized **Wazuh SIEM/XDR** architecture successfully aggregates endpoint telemetry, parses raw authentication logs, and triggers immediate out-of-the-box high-priority alert clusters to mitigate active network intrusion attempts.

---

##  Enterprise Lab Architecture
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

## 🎯 Phase 3: Detection & Threat Hunting Analysis
Wazuh’s native rules engine immediately identified the malicious behavior without requiring custom tuning, demonstrating powerful out-of-the-box enterprise protection.

###  Evidence of Detection
Below is the live forensic telemetry captured during the active triage process:

#### 1. Failed Password Attempts Detected
*This screenshot shows individual failed login events registering in the Wazuh dashboard as traffic spiked.*
![Failed Password Alerts](./docs/failed_logins.png) 

#### 2. Brute-Force Cluster Triggered
*This screenshot captures the specific high-severity alert indicating that an automated brute-force attack was underway.*
![Brute Force Triggered](./docs/brute_force_alert.png)

### 📊 Rule Metrics Validated
*   **Failed Authentication Events:** Detected individual attempts to login using failed credentials or non-existent users.
*   **Brute-Force Cluster Alarms:** Successfully escalated the incident to a high-priority alert due to the rapid frequency of automated attempts hitting the endpoint.

---

## 🛡️ Strategic Hardening & Next Steps
To mitigate the security gaps exposed by this simulation, a production enterprise environment should implement:
1. **Wazuh Active Response:** Configure the central manager to automatically trigger a firewall drop rule on the attacker's IP address upon reaching a threshold of failed attempts.
2. **Network Defenses:** Implement strict account lockout policies, Fail2ban, or shift to mandatory multi-factor authentication (MFA).

---
**Lead Engineer:** Alan (Alancyber)  
