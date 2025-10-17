Benign data <> Malicious Data
![[Pasted image 20250811110114.png]]
## Syllabus
1. **Network Traffic Analysis**: Wireshark  
2. **SIEM**: ELK & Splunk  
3. **SIEM Search Languages**: KQL, etc.  
4. **Event and Logs Correlation**  
5. **Events Analysis**  
6. **Process Analysis and Anomaly Detection**  
7. **Cyber Kill Chain**


---

## Incident Handling

### Definitions

1. **Incident**: Any event that compromises the *Confidentiality*, *Integrity*, or *Availability* (CIA) of information.  
2. **Incident Handling**: The structured set of actions taken when an incident is detected, aimed at minimizing damage and restoring normal operations.  
 3. **NIST Definition**: Security incidents are events that have, or may have, adverse effects on an organization’s operations, assets, or individuals.
- eg: 
	- Unauthorized elevation of privileges to root or admin level.  

---

## Cyber Kill Chain Model
*The Cyber Kill Chain describes the sequential stages of an attacker’s operation, from initial planning to achieving their objectives. Understanding each stage helps defenders detect and disrupt attacks early.*

---

### Attacker’s Perspective

1. **Reconnaissance**  
   - Gathering information about the target, usually passively, to avoid detection.  
   - Examples: OSINT searches, WHOIS lookups, social engineering, social media profiling.  
   - Goal: Identify potential weaknesses, entry points, or high-value targets.
2. **Weaponization**  
   - Preparing a malicious payload designed to exploit a discovered vulnerability.  
   - Often involves pairing malware with an exploit (e.g., malicious macro in a Word document).  
   - This stage happens **offline**, before contact with the target.
3. **Delivery**  
   - Transmitting the malicious payload to the target.  
   - Common methods: phishing emails, infected USB drives, malicious websites, or drive-by downloads.  
   - Goal: Reach the target without raising suspicion.
4. **Exploitation**  
   - Triggering the vulnerability to run the attacker’s code.  
   - Can be automatic (drive-by exploit) or require user action (opening a malicious file).
5. **Installation**  
   - Installing malware or creating persistence mechanisms (backdoors, rootkits) to maintain access.  
   - Goal: Survive system reboots, updates, and user actions.
6. **Command & Control (C2)**  
   - Establishing remote communication between the attacker and compromised systems.  
   - Can use HTTP, HTTPS, DNS tunneling, or custom protocols.  
   - Often involves encryption to hide malicious activity.
1. **Actions on Objectives**  
   - Executing the final mission:  
     - **Data theft** (exfiltration)  
     - **Disruption** (denial-of-service)  
     - **Monetization** (ransomware, cryptomining)  
   - This is the ultimate goal of the attack.

---

### Incident Responder’s Perspective
When viewed from a blue team point of view, the stages are often condensed into fewer, action-oriented phases:

1. **Reconnaissance**  
   - Detect early OSINT gathering or suspicious traffic patterns.  
1. **Scanning**  
   - Identify attempts to enumerate services, ports, or vulnerabilities.  
1. **Exploit**  
   - Spot exploitation attempts in logs, IDS alerts, or endpoint behavior.  
1. **Post-Exploitation**  
   - Detect persistence mechanisms, lateral movement, and data exfiltration.  

**Note:**  
Blue teams aim to **detect and interrupt the chain as early as possible** ideally during Recon or Scanning to prevent full compromise.
![[Pasted image 20251008103557.png]]

---

### SOC Playbook
*A SOC (Security Operations Center) playbook is a structured guide for analysts to follow when specific threats or alerts are detected.*

Typical Playbook Components:
- **Detection Triggers**: What event or alert initiates the response.
- **Triage Steps**: How to validate the alert and assess impact.
- **Containment Actions**: Isolate affected systems, block malicious IPs/domains.
- **Eradication Steps**: Remove malicious artifacts, kill processes, revoke credentials.
- **Recovery Procedures**: Restore systems and services to normal operation.
- **Post-Incident Review**: Document findings, update playbooks, and improve detection rules.


--- 

### Incident Handling Process Phases

1. **Preparation**  
   - Establish Policies + Response Procedures (Documentation), Tools, and Trained Teams (Multi-disciplinary team) before incidents occur.  
   - Maintain updated playbooks, backup procedures and chain of custody of actions.
   - **Develop a breach/incident communication protocol** outlining reporting hierarchy and escalation procedures. (who reports to who ?) known as the definition of a SPOC (Single Point of contact) & Effective Reporting
   - Determine **Minimum Time** to respond & schedule
   - **Incident handler MUST have unrestricted access to ALL defense systems.**

 2. **Detection & Analysis** 
   - Identify suspicious events through monitoring tools or SIEM alerts & Categorize findings by severity and impact. (Correlate multiple data sources for context)
   - Analyze logs, traffic, and endpoints to confirm the incident.  
   - Define a Primary Incident Handler (Main contact during incidents)
   - Be ready to switch to an alternative Trust & Secure Communication tool in case of compromise 
   - Baselines & Visibility: Define normal behavior for network, hosts, apps and eetect deviations quickly.  
   - Define Logical Detection Levels:
	   - Network Perimeter: Firewalls, internet-facing NIDS, IPS, DMZ.
	   - Host Perimeter: Host firewalls, EDRs.
	   - Host-Level: OS logs, process monitoring. 
	   - Application-Level: App logs, API monitoring.

3. **Containment, Eradication & Recovery**  
   - **Before Containment** : Tools such as **RTIR** & **Cortex XSOAR** should be in place to consolidate all tickets and information for more effective response activities. (= Incident Tracking Mechanism)
![[Pasted image 20250818155928.png]]
[!] Don't let the attacker know that you're aware that he's in [!]
INSTEAD,  and as an additional defense measure to track attackers, you could use [[http://canarytokens.org/generate]] to generate documents that call back to a designated email or webhook URL the moment they are accessed, mentioning the source IP address and other information
   - **Containment**: Isolate affected systems ; Preventing an incident from getting worse. (eg:  preventing the intruder from getting any deeper)
   - **Eradication**: Remove malicious files & processes ; Eliminating intruder artifacts ;  understanding the root cause, attack vectors and TTPs.
   - **Recovery**: Restore systems to normal operation & patch vulnerabilities.

4. **Post-Incident Activity**  (Lesson Learned)
   - Document the incident timeline, actions taken, and lessons learned.  
   - Improve defenses and update response procedures based on findings.

![[Pasted image 20250813143654.png]]

### **Incident Handling VS Incident Response**
![[Pasted image 20251008092008.png]]


### **SOC Services: Proactive VS Reactive**
![[Pasted image 20251008143859.png]]

### **Key Functions of a SOC**
![[Pasted image 20251008145135.png]]

### **Interaction of Roles Within a SOC**
![[Pasted image 20251008163303.png]]


### **Interaction Between SOC & Red Team**
Red Team simulates real-world attacks & TTPs (Tactics, Techniques and Procedures) on organization's security defense.
![[Pasted image 20251009080917.png]]
Example Scenario: 
![[Pasted image 20251009081233.png]]

### **Interaction Between SOC & Threat Intelligence Team**
![[Pasted image 20251009081508.png]]

![[Pasted image 20251009081553.png]]

Example Scenario: 
![[Pasted image 20251009081729.png]]






### ****