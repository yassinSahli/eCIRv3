Important Definitions: 
- Artifacts: Traces left by an attacker.(Log entries ; registry keys ; files etc..)

A) Network Level Detection
I. Define a Network Perimeter without SIEM tools: 
- Using wireshark, we're going to target scom fortinet gateway and perform an intensive scan on it, then detect it using wireshark as shown below: 
![[Pasted image 20250814144301.png]]

![[Pasted image 20250814145008.png]]

- If a you receive an alert from you SIEM on a **WINDOWS** host, here's what you should do: 
		1. Run cmd as admin
		2. >  ```netstat -naob  ```
		3. Check for suspicious process / ports
	![[Pasted image 20250815151108.png]]

[!] A suspicious looking executable named winlogin.exe is listening on port 9999 [!]

- If a you receive an alert from you SIEM on a **LINUX** host, here's what you should do: 
	- Scenario: Suppose now, that you were informed about other organizations of your industry being targeted by a Linux malware that utilizes port 22 for its communications. Let’s proactively check the network and internet connections of a critical Linux-based host.
		1.  ```lsof  -i :22 ```
			-> Returns nothing: DO NOT CONCLUDE FROM THIS POINT -> Use Redendency techniques. 
		2.  ```netstat -anp | grep :22 ```
			-> Result: 
			![[Pasted image 20250818081118.png]]

[!] “lsof” scans the process table, this is why it missed the concealed process and the accompanying open sockets
[!] “netstat” on the other hand, focuses on the open socket list, and only if we instruct it (-p flag), it tries to locate the associated processes in the process table. Regardless of “netstat” finding associated processes or not, it will still output open sockets

-> The bottom line is, “Redundancy” and taking advantage of all the available toolkit is advised

B) Host Level Detection = Analyze Data Residing in the Host.
A/Vs and EDR solutions, as well as the users themselves, can assist in such detection activities. If we have any EDR installed, check the malicious executable by it. 

![[Pasted image 20250818082343.png]]

C) Application Level Detection = Analyze Logs
As an eg of App level detection, spotting abnormal behavior when statistically analyzing IIS logs.

![[Pasted image 20250818085442.png]]
--> Reverse / Bind Shell Discovered 
### Incident Response Process
![[Pasted image 20251010084233.png]]


![[Pasted image 20251009092340.png]]
### **The Incident Response Lifecycle (NIST 800-61)**
![[Pasted image 20251008155435.png]]
The life cycle is divided into **4 main phases**: 

	+ Preparation: involves establishing policies, procedures, tools, and resources necessary to effectively detect and respond to incidents

	+ Detection & Analysis: identifying potential security incidents and analyzing them to confirm their legitimacy and impact

	+ Containment, Eradication & Recovery: focuses on stopping the attack, once an incident is confirmed and involves eliminating the threat, and restoring systems to normal operations.

	+ Post-Incident Activity (Lessons Learned): ensures that lessons are learned from the incident to improve future detection and response capabilities.

### Event Triage and Investigation

**Event triage** is the process of **analyzing and categorizing security alerts or events** to determine whether they indicate a **potential security incident, a false positive, or a benign activity.**

The goal is to quickly assess the relevance and severity of an event and decide on the appropriate next steps.

#### Triage:
![[Pasted image 20251009103323.png]]
#### Investigation Part 1:
![[Pasted image 20251009103645.png]]
#### Investigation Part 2:
![[Pasted image 20251009103810.png]]

### SOAR Workflow: Automated Phishing Response
![[Pasted image 20251009142128.png]]

### Playbooks in SOARs: Malware Detection and Containment
![[Pasted image 20251009142831.png]]
**Splunk SOAR (Phantom) Playbook to Detect Malware and Contain it:**
```yaml
playbook:
  name: "Auto_Malware_Detection_and_Containment"
  label: "Automated Malware Threat Response"
  description: >
    Automatically detect, enrich, contain, remediate, and recover from malware threats on endpoints.
    Triggered by alerts from EDR or SIEM sources.

  triggers:
    - type: "new_event"
      conditions:
        - source_product: ["EDR", "SIEM"]
        - category: ["Malware Detected", "Suspicious File", "Endpoint Infection"]

  steps:

    - name: "1_Ingest_Alert"
      action: "collect"
      input:
        fields: ["device_hostname", "username", "file_hash", "file_path", "process_name", "src_ip"]
      output: "alert_data"

    - name: "2_Automated_Enrichment"
      action: "threat_intel_lookup"
      input:
        hash: "{{ alert_data.file_hash }}"
        sources: ["VirusTotal", "HybridAnalysis", "MISP"]
      output: "hash_reputation"

    - name: "3_Get_Endpoint_Context"
      action: "endpoint_query"
      input:
        hostname: "{{ alert_data.device_hostname }}"
        query: "list_running_processes, network_connections, recent_files"
      output: "endpoint_context"

    - name: "4_Identify_Threat_Source"
      action: "analyze_origin"
      input:
        context: "{{ endpoint_context }}"
        indicators: "{{ hash_reputation }}"
      output: "infection_vector"

    - name: "5_Containment_Isolate_Endpoint"
      action: "endpoint_isolate"
      input:
        hostname: "{{ alert_data.device_hostname }}"
      condition:
        - if: "{{ hash_reputation.malicious == true }}"
          then: "isolate"
      output: "containment_status"

    - name: "6_Block_C2_Communication"
      action: "block_indicator"
      input:
        iocs: "{{ hash_reputation.domains + hash_reputation.ips }}"
        devices: ["Firewall", "Proxy"]
      output: "block_status"

    - name: "7_Remediation_Scan"
      action: "malware_scan"
      input:
        hostname: "{{ alert_data.device_hostname }}"
      output: "scan_results"

    - name: "8_Remove_Or_Quarantine"
      action: "remove_file"
      input:
        hostname: "{{ alert_data.device_hostname }}"
        file_path: "{{ alert_data.file_path }}"
      condition:
        - if: "{{ scan_results.infected == true }}"
          then: "quarantine"
      output: "remediation_status"

    - name: "9_Recovery_And_Validation"
      action: "endpoint_reconnect"
      input:
        hostname: "{{ alert_data.device_hostname }}"
      condition:
        - if: "{{ remediation_status.success == true }}"
          then: "reconnect"
      output: "recovery_status"

    - name: "10_Final_Verification"
      action: "endpoint_verify_clean"
      input:
        hostname: "{{ alert_data.device_hostname }}"
      output: "validation_result"

    - name: "11_Documentation_And_Reporting"
      action: "create_incident_report"
      input:
        details:
          alert: "{{ alert_data }}"
          enrichment: "{{ hash_reputation }}"
          containment: "{{ containment_status }}"
          remediation: "{{ remediation_status }}"
          recovery: "{{ recovery_status }}"
      output: "report_id"

    - name: "12_Notify_SOC"
      action: "send_notification"
      input:
        message: "Malware incident on {{ alert_data.device_hostname }} contained and remediated. Report ID: {{ report_id }}"
        recipients: ["SOC_Team", "IT_Security_Lead"]

  end_conditions:
    - success: "{{ validation_result.clean == true }}"
    - failure: "{{ validation_result.clean == false }}"
```

**Example Playbooks:**
**[!]GOLD[!] https://gitlab.com/syntax-ir/playbooks [!]GOLD[!]**
- https://github.com/counteractive/incident-response-plan-template
- https://github.com/socfortress/Playbooks
- https://www.cisa.gov/sites/default/files/2024-08/Federal_Government_Cybersecurity_Incident_and_Vulnerability_Response_Playbooks_508C.pdf

### Events and Incidents
![[Pasted image 20251009162905.png]]
![[Pasted image 20251009163957.png]]
![[Pasted image 20251009164216.png]]