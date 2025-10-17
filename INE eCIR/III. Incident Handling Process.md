# A) Short Term Containment: 
To do so, we can disable network connectivity or even disconnect the machine from the power line, in extreme occasions.

 As an additional defense measure to track attackers, you could use http://canarytokens.org/generate to generate URLs that you can configure in your local network. Once accessed it calls back to a designated email mentioning the source IP address and other information.

 Step 1:  Hop-in to  http://canarytokens.org/generate
![[Pasted image 20250819111507.png]]
Step 2: Create a web bug token to be triggered when the generated link is clicked
![[Pasted image 20250819111657.png]]
Step 3: Send to someone by a simple social engineering technique "is this link accessible to you ?" and wait for them to click 
![[Pasted image 20250819111920.png]]![[Pasted image 20250819112025.png]]

---

# B) System Backup & Imaging 
The next step is to make images of the affected system(s) for forensics activities.

-> **To preserve the evidence, you’re not supposed to work on the original machine when investigating and you’re also not supposed to analyze and work on the first image you take**

eg Tools: FTK Imager, EnCase, Autopsy

[!] *Imaging = Data Acquisition & Retrieval == Volatility* [!]

The storage mediums can be arranged from the most volatile to the least
![[Pasted image 20250819112952.png]]
- Note that: 
	[!] Volatile Data = Temporary Data that vanishes if system is down [!] such as running processes and open applications & RAM contents & Temporary files stored in memory.
	
	[!] Not Volatile Data = Persistent Data [!] such as Hard drive files & System backups.

#### B.1) Static Acquisition: The **DATA** that will not be affected by system restart (=**NOT VOTILE**)
	--> Usually performed when system is *powered off* 
	--> Integrity over volatility
#### B.2) Dynamic Acquisition 
	--> Usually performed when system is *powered on*
	--> Volatility over Integrity 

[!] The **Imaging** operation is not always performed; if the system is compromised by a rootkit, an acquisition in this case may be dangerous, this is what we call a **dead acquisition** [!] 

- _Live response_ = running system, volatile evidence collected.
- _Dead response_ = powered off, forensic image taken

Note: 
- A backdoor is the secret entrance.
- A rootkit is the cloak that hides you inside. ( See Assassin’s Creed Valhalla screenshot below)
![[Pasted image 20250819133848.png]]

#### B.3) Write Blockers 
The risk of altering the original evidence while acquiring data is high. For this reason, many tools/software exist that can assist us in acquiring data in a safer manner: 
- Write Blockers achieve this, as their name suggests, by blocking the hard disk from writing.
- Write blockers could either be hardware-based or software- based.
![[Pasted image 20250819152014.png]]

#### B.4) Evidence Integrity: 
There should be a way to validate the integrity of the acquired evidence: **Hash Functions** 
Hash functions are One Way Cryptographic Functions which map data of arbitrary size to a bit string of a fixed size:
- The slightest change to the source file will result in a totally different hash.
- All calculated hash strings should be stored and communicated safely since they will be used to prove that the acquired data has not been altered.

---

# C) Long-Term Containment 
Before containment actions, **coordinate with your ISP** in cases of:
- **DDoS attacks**
- **Worms**
- **Phishing campaigns**

[!] ISPs provide greater visibility into attacks and maintain useful logs [!]

#### C.1) Decide Containment Approach
- If attacker’s **actions/motives are unclear**, consider **keeping the system intact** to monitor their next moves.  
- The containment decision **must go through the respective business unit manager(BUM):
	  - Critical systems cannot be shut down easily since they support core business processes.
	  - If the manager **approves system shutdown**, proceed directly to **Eradication**.
	  - If the system **must remain operational**, apply **long-term containment measures**.

#### C.2) Long-term Containment Activities
- Patch affected and related systems  
- Deploy **HIDS/NIDS**  
- Change **passwords** and revoke/modify **trust relationships**  
- Apply additional **firewall/router rules** (ingress/egress)  
- Block/drop packets tied to incident-related IPs  
- Remove or restrict attacker access 

⚠️ Long-term containment should begin **after imaging and evidence acquisition** are completed.

---

# D) Eradication

The goal of **Eradication** is to **completely remove attacker presence** and close exploited paths.

#### D.1) Key Actions
- Identify the **root cause** and **indicators of compromise** (using Detection & Analysis data).  
- Isolate the **intrusion** and determine the **attack vector**.  
- Perform **drive-wiping** before reinstallation or recovery.  
- **Simply reformatting or restoring backups is *not sufficient.**

#### D.2) Eliminating Attacker Residuals
- Remove **malware, backdoors, rootkits, kernel-mode drivers**, etc.  
- If a **rootkit** is detected:
	  - Zero the drive
	  - Reformat and rebuild the system from **trusted media**  
- Analyze logs for **credential reuse** (Remote Desktop, SSH, VNC, etc.).

#### D.3) Improving Defenses
- Configure **additional router & firewall rules**  
- Obscure the affected system’s network visibility  
- Apply **null routing** for malicious traffic  
- Implement:
  - System hardening  
  - Regular patching  
  - Vulnerability assessment procedures  

⚠️ Other systems in the network may share the same vulnerability — **scan and secure them as well**.

---

# E) Recovery

After **Eradication** is complete, the next step is **Recovery**.  
This phase focuses on **bringing the affected system(s) back into production** safely, in coordination with business units.
#### E.1) Recovery Process

1. **System Restoration**
   - Restore the affected system(s) and prepare them for production use.

2. **Business Unit Quality Assurance**
   - Request the **business unit** to perform QA activities.
   - Ensure the system is fully functional and contains everything needed for their operations.

3. **Decision on Production Re-entry**
   - Work with the **business unit manager/representative** to decide when the system is ready to re-enter production.

![[Pasted image 20250820101900.png]]

--- 
# F) Post-Incident Activity

The final step of the Incident Handling Process is **Post-Incident Activity**.  
This phase is critical because attackers are constantly evolving, and incident handling is never trivial.  

#### Reporting Weaknesses and Strengths
- Report all **identified weaknesses, oversights, and blind spots** discovered during the incident.  
- Do not only highlight failures — also include:
  - **Working processes** that proved effective  
  - **Successful detection methods**  
  - Any **preventive or responsive measures** that worked against specific stages of the attack  

Be objective, accurate, and thorough in your reporting.

#### Lessons Learned Report
- Right after **Recovery**, the incident handling team should draft a **Lessons Learned Report**.  
- The report must cover:
	  - Incident timeline  
	  - Attack methods observed  
	  - Response actions taken  
	  - Effectiveness of containment, eradication, and recovery efforts  
	  - Recommended improvements  

#### Post-Incident Meeting
- Once things stabilize, **schedule a meeting** with all relevant stakeholders:
	  - System administrators  
	  - Affected business unit representatives  
	  - IT security team  
	  - Incident response team members  
	  - SPOC (Single Point of Contact)
- Use the meeting to **review the report, share insights, and agree on improvements**.


What to look for during the weeks (or even months) to come:
	• Changes to registry keys and values. reg \\[MachineName]
	• Abnormal processes via wmic. wmic /node:[MachineName]/user:[Admin] /password:[password] or ps for Linux
	• Abnormal user accounts. wmic useraccount list brief or net
user commands or cat /etc/passwd for Linux
#### Post-Incident Activity Summary
- **Document** both strengths and weaknesses.  
- **Produce a Lessons Learned Report** immediately after recovery.  
- **Hold a Post-Mortem Meeting** with all stakeholders.  
- **Focus on continuous improvement** of processes, technologies, and visibility.  


---

# Conclusion: 

- **Containment:** Limit attacker activity without disrupting business operations unnecessarily.  
- **Eradication:** Fully remove attacker presence and improve security posture.  
- **Recovery:** Safely restore systems to production and monitor for issues.  

**Expert Notice: Organise Your IR Toolkit**
![[Pasted image 20251010144452.png]]

Responder Toolkit:
![[1 1.png]]
![[3.png]]
![[4.png]]
