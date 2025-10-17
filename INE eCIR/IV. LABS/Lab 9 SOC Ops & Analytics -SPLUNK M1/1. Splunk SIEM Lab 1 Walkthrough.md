Splunk is a solution to aggregate, analyze and get answers from machine data. Splunk can be used for Application Management, Operations Management, Security & Compliance, etc. 

It can literally ingest almost any data from almost any source, through both an agent-
less and a forwarder approach.

![[Pasted image 20250930105138.png]]

# Splunk Architecture and Core Concepts
![[Pasted image 20250930105159.png]]
## Main Components

### 1. Forwarder
- **Universal Forwarder (UF)**:  
	  - Collects data from remote sources.  
	  - Lightweight, minimal impact on performance.  
	  - Sends data to one or more indexers.  

- **Heavy Forwarder (HF)**:  
	  - Collects and **parses data** before forwarding.  
	  - Can route data based on source/type.  
	  - Can index locally and forward to another indexer.  
	  - Used for **data aggregation, API/scripted access**.  
	  - Only compatible with **Splunk Enterprise**.  

- **HTTP Event Collector (HEC)**:  
	  - Collects data directly from apps.  
	  - Uses **token-based JSON/raw API**.  
	  - Sends data straight to indexers.  
### 2. Indexer
- Processes and **indexes machine data**.  
- Stores data as **compressed raw data + indexes**.  
- Enables **fast search and analysis**.  
### 3. Search Head
- Provides interface for searching indexed data.  
- Distributes search requests to indexers and consolidates results.  
- Extracts fields and allows creation of **knowledge objects**.  
- Enhances searches with **reports, dashboards, and visualizations**.  

---
## Splunk Apps & Technology Add-ons (TAs)
- **Splunk Apps**:  
	  - Collections of inputs, UI, and knowledge objects.  
	  - Provide workspaces for different use cases/roles.  
	  - Available on [Splunkbase](https://splunkbase.com).  
- **Technology Add-ons (TAs)**:  
	  - Abstract data collection methods.  
	  - Include field extractions, config files, and scripts.  
	  - Apps typically use one or more TAs.  

---
## Splunk Users and Roles
- **admin**: Full capabilities.  
- **power**: Can edit shared objects, alerts, and tag events.  
- **user**: Can run searches, save their own searches, create event types, etc.  

---
## Search & Reporting App
- Main interface for most Splunk usage.  
- **Data Summary**: Shows hosts, sources, sourcetypes.  
- Provides visualization of events.  
- You will spend most of your time inside Splunk’s Search & Reporting App
![[Pasted image 20250930163546.png]]
![[Pasted image 20250930163621.png]]
![[Pasted image 20250930163657.png]]

---
## Splunk’s Search Processing Language (SPL)
- Mix of **SQL + Unix pipeline**.  
- Designed for **time-series event data**.  
- Same language used for **queries and visualizations**.  
- Over **140 commands** for searching, correlating, analyzing, and visualizing.  
![[Pasted image 20250930163519.png]]
![[Pasted image 20250930163745.png]]
### SPL Components
1. **Search terms** – keywords, phrases, Booleans.  
2. **Commands** – operations (charts, stats, etc.).  
3. **Functions** – how to process/compute data.  
4. **Arguments** – variables applied to functions.  
5. **Clauses** – grouping, renaming, filtering fields.  

---
## Search Modes
- **Fast**: Field discovery OFF, minimal data.  
- **Smart**: Default mode, balanced.  
- **Verbose**: Full event & field data.  
- Recommended: Start with **Smart Mode**.  

---
## Resources
- **Exploring Splunk e-book** (esp. Chapter 4).  
- [Splunk Search Documentation](https://docs.splunk.com/Documentation/Splunk/7.2.4/Search/GetstartedwithSearch)  
- Lab:
![[Pasted image 20251003153353.png]]
#### **Scenario**: 
The organization you work for (Wayne Enterprises) is using Splunk as a SIEM solution toenhance its intrusion detection capabilities. The SOC manager informed you that theorganization has been hit by an APT group. He tasked you with responding to this incidentby heavily utilizing Splunk and all the data that it ingested.The data that Splunk has ingested consist of Windows event logs, Sysmon logs, Fortinetnext-generation firewall logs, Suricata logs, etc.


#### **Objectives**
The learning objective of this lab is to not only get familiar with Splunk’s architecture and detection capabilities but also to learn effective Splunk search writing. Specifically, you will learn how to use Splunk’s capabilities in order to: 
- Have better visibility over a network 
- Respond to incidents timely and effectively 
- Proactively hunt for threats
- Output a Valid Professional Cyber Kill Chain
![[Pasted image 20250930143332.png]]

#### **Network Configuration**
- Incident Responder’s Subnet: 172.16.72.0/24
- Splunk: 172.16.72.100:8000
- Connection Type: Web-based
Use a Chrome or Firefox browser to connect to Splunk's web interface
(http://172.16.72.100:8000), as follows
- Username: admin
- Password: els@nalyst

#### Task 1: Identify any Recon Activities Against your Network Through Splunk Searches in imreallynotbatman.com website
##### Hints:
- Focus on the stream:http sourcetype and identify the source IPs that are responsible for the majority of the traffic. Then, validate your findings using the suricata sourcetype.
- Move the investigation deeper by analyzing all important fields and sourcetypes
#### Workload: 
Once you are logged into Splunk’s web management interface, click the **Search &
Reporting application** that resides on the Apps column on your left:
![[Pasted image 20250930110341.png]]
[!] NOTE: Before starting your investigation change the time range picker to All time [!]

In order to test if Splunk can successfully access the ingested/loaded data, first change the time range picker to **All time** and then, submit the following search: 

``SPL> index="botsv1" earliest=0``

![[Pasted image 20250930110500.png]]
Now that we know everything worked as expected, let’s identify any reconnaissance
activities against Wayne Enterprises: 
the organization’s website: **imreallynotbatman.com**

##### Step 1: Determine target sourcetypes to search associated with imreallynotbatman.com

``SPL> index=botsv1 imreallynotbatman.com

![[Pasted image 20250930112443.png]]
[!] THE "sourcetype" field is NOT FOUND BY DEFAULT, you should add it [!]

![[Pasted image 20250930112510.png]]

##### Step 2: Let’s identify all source addresses
![[Pasted image 20250930112622.png]]
Since we are interested in identifying reconnaissance activities, it would be better to focus
on the stream:http sourcetype. 

```
List of Suspicious Hosts = [ 40.80.148.42 ; 192.168.250.70 ; 23.22.63.114  ]
```

*We should note that 192.168.250.70 is a private address.

``SPL> index=botsv1 imreallynotbatman.com sourcetype=stream:http

[!] Note: Stream is a free app for Splunk that collects wire data and can focus on a number of different protocols including smtp, tcp, ip, http... [!]

##### Step 3: Focus on HTTP Traffic only 
``SPL> index=botsv1 imreallynotbatman.com sourcetype=stream:http

After that, hit the ``src`` field (Since attackers usually originate from **external source IPs**, so this will surface which external hosts are making HTTP requests to `imreallynotbatman.com`) 

And just like that, our scope of suspicion will be narrowed down to only 2 IPs: 40.80.148.42 and 23.22.63.114.

You should take note that 40.80.148.42 is associated with ~95% of the http traffic.

```
List of Suspicious Hosts = [ 40.80.148.42 ; 23.22.63.114 ]
```

An alternative way to identify all sources is the following:

``SPL> index=botsv1 imreallynotbatman.com sourcetype=stream* | stats count(src_ip) as Requests by src_ip | sort - Requests

![[Pasted image 20251003115019.png]]

##### Step 4: In-Depth Investigation of Host 40.80.148.42 (~95% HTTP Traffic)

So far, we can only assume that 40.80.148.42 was the IP from where the APT group
performed its reconnaissance activities. We can validate this finding, by checking with Suricata, as follows:

``SPL> index=botsv1 imreallynotbatman.com src=40.80.148.42 sourcetype=suricata

We see Suricata logs related to 40.80.148.42, but no ``signature`` field. 
We can see the signatures by scrolling down, clicking on more fields and choosing signature. If we do so, the signature field will be visible under the **Selected Fields** column.
![[Pasted image 20251003151310.png]]
And then select it: 
![[Pasted image 20250930112833.png]]
From the Suricata signatures that were triggered, we can conclude that 40.80.148.42 was
actually scanning ``imreallynotbatman.com``.

##### Step 5: Determine the adversary’s level of sophistication. 

So the question that arises is, did the APT group use known or sophisticated scanning techniques? 

``SPL> index=botsv1 src=40.80.148.42 sourcetype=stream:http

The detailed request information can be found inside the **src_headers** field:
![[Pasted image 20250930113213.png]]
By querying traffic from the attacker’s IP (`40.80.148.42`) in Splunk, analysts discovered that the **APT group leveraged Acunetix WVS** to probe the environment.
The APT group utilized an instance of the reputable **Acunetix vulnerability scanner.** 

We could have Identified the usage of this tool by looking for uncommon user agents in the ``http_user_agent``.
![[Pasted image 20250930113420.png]]
The use of Acunetix via the **unique User-Agent string: acunetix_wvs_security_test** which is a clear indicator of acunetix web scanner tool usage.

We can easily identify which server was the target through the **dest** field on the same search query:
![[Pasted image 20250930113448.png]]
This confirms that the main **target server was 192.168.250.70**, which received nearly all malicious requests.

**Assessment of Sophistication:**
- Using Acunetix indicates a **reliance on common vulnerability scanners** rather than advanced or stealthy custom tools.
- This suggests the adversary’s approach here was **not highly sophisticated**,

##### Step 7: Determine what has been requested by the APT group

Since the target was our web server ``192.168.250.70`` we can execute the following query to determine the requested directories by the attacker: 

``SPL> index=botsv1 dest=192.168.250.70 sourcetype=stream:http

The URLs being requested can be found inside the **uri** field:
![[Pasted image 20250930135812.png]]

We are also interested in successful page loads. We can identify them, as follows.
``SPL> index=botsv1 dest=192.168.250.70 sourcetype=stream:http status=200 stats count by uri | sort - count

Note: The transformational search command called **stats** will allow us to count the number of events grouped by URI.

![[Pasted image 20250930135909.png]]

We could have achieved similar results through the iis sourcetype, as follows:
``SPL> index=botsv1 sourcetype=iis sc_status=200 | stats values(cs_uri_stem)

![[Pasted image 20250930141059.png]]

You may be wondering why we aren’t specifying 192.168.250.70 ;
This is because if we submit the query below and check the host field, we will find only one host, **we1149srv = 192.168.250.70**
![[Pasted image 20251003153102.png]]

``SPL> index=botsv1 sourcetype=iis
![[Pasted image 20250930141221.png]]

##### Step 7: Update the Cyber Kill Chain with latest Findings
![[Pasted image 20250930143514.png]]


#### Task 2: Identify any Weaponization Activities on the Network  

==Expert Note: During our investigations, not every answer can be found within the SIEM. There will be times when we will need to pivot from the SIEM to other internal or open sources to find answers.==

We are interested in identifying domains that are pre-staged to attack **Wayne Enterprises**.  

List of Suspicious Hosts = [ 40.80.148.42 ; 23.22.63.114 ]  

We already analyzed the **40.80.148.42** IP in Splunk.  
Now, let’s investigate **23.22.63.114** through open sources, since Splunk doesn’t contain much information about it.  

---

##### Step 1: Submit the suspicious IP (23.22.63.114) to Robtex  
Go to [http://www.robtex.com](http://www.robtex.com) and submit the **23.22.63.114** IP.  

![[Pasted image 20250930142859.png]]  

As we can see, this IP has a number of other domain names associated with it.  
These domain names are most probably **phishing domains** since their name is similar to the organization we work for, Wayne Enterprises.  

---

##### Step 2: Use additional open sources for enrichment  
Open sources like [https://threatcrowd.org](https://threatcrowd.org) and [https://www.virustotal.com](https://www.virustotal.com) can provide additional information.  

![[Pasted image 20250930142929.png]]  

For example, through **threatcrowd.org**, we identified additional domains associated with the APT group by submitting the 23.22.63.114 IP.  

---

##### Step 3: Investigate whois information of suspicious domains  
Remember when we talked about whois information and how attackers leverage them for targeted attacks?  
Well, let’s give attackers a taste of their own medicine by checking the whois information of every associated domain.  

While checking the whois information of **wayncorpinc.com**, we come across the following:  
![[Pasted image 20251003154305.png]]![[Pasted image 20251003154320.png]]

![[Pasted image 20250930143825.png]]  

---

##### Step 4: Conduct reverse email searches  
We can then proceed to **reverse email searches** and possibly identify additional infrastructure associated with the APT group.  

Find an example of a reverse email search below:  
[https://www.threatcrowd.org/email.php?email=LILLIAN.ROSE@PO1S0N1VY.COM](https://www.threatcrowd.org/email.php?email=LILLIAN.ROSE@PO1S0N1VY.COM)  
OR
https://mailmeteor.com/email-checker?email=LILLIAN.ROSE%40PO1S0N1VY.COM
![[Pasted image 20251003154637.png]]

---

##### Step 5: Update the Cyber Kill Chain  
Here’s our cyber kill chain so far:  

![[Pasted image 20250930143444.png]]

#### Task 3: Identify any Delivery Activities on the Network  

The main task currently is to know as much as possible about this APT group’s **TTPs** and used malware.  
For that, we will leverage **open sources** to expand our knowledge of their delivery phase.  

---

##### Step 1: Search the suspicious IP on ThreatMiner  
- Navigate to https://www.threatminer.org.  
- Submit the suspicious IP: **23.22.63.114**.  
- ThreatMiner has the capability to link related malware samples to IP addresses.  

![[Pasted image 20251001151302.png]]  
*Explanation:* ThreatMiner results for **23.22.63.114** displaying related malware samples and indicators. These samples provide insight into the tools the APT group is using for delivery. 

We should collect the HASH associated with the suspicious IP: ``c99131e0169171935c5ac32615ed6261``

---

##### Step 2: Investigate malware samples on VirusTotal  
- From ThreatMiner’s output, collect malware **MD5 hashes**.  
- Submit them to [https://www.virustotal.com](https://www.virustotal.com).  

**Hash to submit:**  
``c99131e0169171935c5ac32615ed6261

![[Pasted image 20251001151517.png]]  
![[Pasted image 20251001151606.png]]  

*Screenshot explanation:* VirusTotal reports showing metadata about the submitted hash.  
This includes antivirus detections, malware families, behavior analysis, and other related samples.  

---

##### Step 3: Identify persistence in filenames  
- Note that malware hashes may change (mutants), making detection harder.  
- However, **filenames** often remain consistent and can be tracked across campaigns.  

**Filename discovered:**  
``MirandaTateScreensaver.scr.exe


*Inline note:* Keep filenames as additional IoCs since adversaries may reuse them even if hashes change.  

---

##### Step 4: Update the Cyber Kill Chain  
- Add the discovered **malware delivery method** (suspicious executable delivery via phishing infrastructure) to the Cyber Kill Chain model.  
- This documents the transition from **weaponization** to **delivery**.  

![[Pasted image 20251001151814.png]]  
*Screenshot explanation:* Updated cyber kill chain showing delivery stage with the identified malware sample.  

---

##### Step 5: Final deliverables and recommendations  
- Document all hashes, filenames, and associated IP/domain infrastructure.  
- Correlate VirusTotal/ThreatMiner results with endpoint or proxy logs to identify if the malware was delivered inside the environment.  
- Create detection signatures for known filenames (e.g., `MirandaTateScreensaver.scr.exe`) and enrich SIEM detection content.  

-> Splunk SPL Queries: 
```
Search for the MD5 hash across file monitoring logs:
SPL> index=botsv1 sourcetype=endpoint_logs "MirandaTateScreensaver.scr.exe"


Search for IP/domain communication attempts (delivery C2 channels):
SPL> index=botsv1 (src=23.22.63.114 OR dest=23.22.63.114) sourcetype=stream:http

Search DNS logs for phishing domains tied to 23.22.63.114:
SPL> index=botsv1 sourcetype=dns (query="*wayncorpinc.com" OR query="_relatedphishingdomain_")  
```


#### Task 4: Identify any Exploitation Activities on the Network  

Now it is time to pivot back to **Splunk** to identify possible **exploitation activities**.  
We will focus on **HTTP POST requests**, since logins and credential submissions are typically sent using POST.  

---

##### Step 1: Identify source IP addresses generating the largest number of POST requests  
Execute the following search to list POST events directed at the web server `192.168.250.70`:  
``SPL > index=botsv1 sourcetype=stream:http dest="192.168.250.70" http_method=POST

![[Pasted image 20251001162341.png]]  
*Screenshot explanation:* Results show the **src field** containing IP addresses responsible for POST requests. These represent possible login attempts or credential submissions.  

---

##### Step 2: Investigate POST requests from 40.80.148.42  
Filter POST requests by source IP `40.80.148.42`:  

``SPL > index=botsv1 sourcetype=stream:http dest="192.168.250.70" http_method=POST src="40.80.148.42"
![[Pasted image 20251004145126.png]]
*Inline note:* The `form_data` field contains submitted username/password information for POST requests.  
![[Pasted image 20251004145518.png]]
Result: **No signs of successful exploitation** from this IP.  

---

##### Step 3: Investigate POST requests from 23.22.63.114  
Now, check the second suspicious host `23.22.63.114`:  

``SPL> index=botsv1 sourcetype=stream:http dest="192.168.250.70" http_method=POST src="23.22.63.114"
![[Pasted image 20251004145600.png]]
*Inline note:* The `form_data` field contains submitted username/password information for POST requests.  
![[Pasted image 20251001162720.png]]  
*Explanation:* Results confirm that **23.22.63.114 is brute-forcing the web server’s authentication form.**  

---

##### Step 4: Re-verify brute-force activity  
Run the following query to confirm brute-force attempts using POST form data fields (`username` and `passwd`):  

``SPL > index=botsv1 sourcetype=stream:http dest="192.168.250.70" http_method=POST form_data=_username_passwd* | stats count by src
![[Pasted image 20251001163207.png]]  
*Screenshot explanation:* The counts show multiple repeated POST attempts from `23.22.63.114`, confirming brute-force behavior.  

---

##### Step 5: Determine if the brute-force attack was successful  
We need to extract and analyze the passwords attempted. If a password is reused multiple times, it likely means the attacker **found valid credentials**.  

``SPL > index=botsv1 sourcetype=stream:http form_data=_username_passwd* dest_ip=192.168.250.70 | rex field=form_data "passwd=(?<userpassword>\w+)" | stats count by userpassword | sort - count

![[Pasted image 20251004150031.png]]  
*Explanation:* The search extracts every `userpassword` from the POST data and counts frequency. **Passwords appearing more than once == successful compromise and reuse by attacker.  **

---

##### Step 6: Identify time of compromise and targeted URIs  
Run the following search to map when the compromise occurred and which URIs were targeted:  

``SPL > index=botsv1 sourcetype=stream:http form_data=_username_passwd* dest_ip=192.168.250.70 src=40.80.148.42 | rex field=form_data "passwd=(?<userpassword>\w+)" | search userpassword=* | table _time uri userpassword


![[Pasted image 20251001163912.png]]  
*Screenshot explanation:* Table output showing timestamps, URIs accessed, and the compromised userpasswords attempted during brute force.  

---

##### Step 7: Confirm successful logins  
Finally, let's determine whether the same IP used these credentials or whether multiple different IPs did. We can verify if successful logins occurred by isolating specific passwords used:  

``SPL > index=botsv1 sourcetype=stream:http | rex field=form_data "passwd=(?<userpassword>\w+)" | search userpassword=batman | table _time userpassword src


![[Pasted image 20251001163948.png]]  
*Explanation:* Two successful login attempts with the password `batman` are visible, along with their timestamps and source IPs.  

---

##### Step 8: Update the Cyber Kill Chain  
Add the brute-force exploitation attempt and confirmed successful logins to the cyber kill chain.  

![[Pasted image 20251001164018.png]]  
*Explanation:* Updated kill chain now showing the **Exploitation phase**, where the adversary successfully brute-forced credentials to gain access.  



#### Task 4: Identify any Installation Activities on the Network  

**Note:** Installation == Post-Exploitation

**Objective:** In the installation phase of the Cyber Kill Chain, we are primarily interested in spotting signs of malware being uploaded to the victim machine. This can be detected by examining both `stream:http` and `suricata` events in Splunk, then extracting hashes via Sysmon events.

---

##### Step 1: Detect uploaded executables via HTTP (`stream:http`)  
**Query-1:**  

``SPL> index=botsv1 sourcetype=stream:http dest="192.168.250.70" *.exe
![[Pasted image 20251004150632.png]]
![[Pasted image 20251002164213.png]]  
*Explanation:* Add the `part_filename{}` field to the displayed fields — it contains the uploaded file names. The query returned an executable named **3791.exe**.  
**Findings:** `3791.exe`

---

##### Step 2: Confirm upload via Suricata (`suricata` sourcetype)  
**Query-2:**  
``SPL> index=botsv1 sourcetype=suricata (dest=imreallynotbatman.com OR dest="192.168.250.70") http.http_method=POST .exe
![[Pasted image 20251004150656.png]]
![[Pasted image 20251002164333.png]]  
*Explanation:* The `fileinfo.filename` field shows uploaded filenames captured by Suricata. This confirms that **3791.exe** was posted to the web application and is very likely the uploaded malware.

**Identify the attacker IP (source of upload):** 
``SPL> index=botsv1 sourcetype=suricata dest_ip="192.168.250.70" http.http_method=POST .exe
![[Pasted image 20251004150856.png]]
![[Pasted image 20251004150917.png]]  
*Explanation:* Suricata events show the source IP that uploaded the file to `192.168.250.70`. Use this to link the upload to the attacker host.

---

##### Step 3: Search logs for the filename to find related telemetry  
**Query:**  
``SPL> index=botsv1 3791.exe
![[Pasted image 20251004151129.png]]
![[Pasted image 20251002164513.png]]  
*Explanation:* A broad search for `3791.exe` across indexes identifies potential matches in endpoint/host telemetry (e.g., Sysmon). This helps locate the process creation events that include file hashes.

---

##### Step 4: Use Sysmon to extract file hashes and process info  
Sysmon logs (XmlWinEventLog:Microsoft-Windows-Sysmon/Operational) often include MD5/SHA1/SHA256, CommandLine and ParentCommandLine which are useful to confirm execution and source.

**Query (narrow to Sysmon events):**  
``SPL> index=botsv1 3791.exe sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
*Tip:* Add fields `Hashes`, `CommandLine`, `ParentCommandLine` if they are not visible by default.
![[Pasted image 20251004151318.png]]

**Filter for process creation events (EventCode=1) to display the exact times when the malware successfully spawned processes (indicating execution)** 
``SPL> index=botsv1 3791.exe sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational EventCode=1
![[Pasted image 20251004151335.png]]
From the results, we can see that the `3791.exe` malware successfully executed **5 times**.  

---

##### Step 5: Extract the MD5 (or other) hashes from command line/process events  
To get the MD5 hash we search for the filename in the `CommandLine` and request the `md5` field values:
``SPL> index=botsv1 3791.exe CommandLine=3791.exe | stats values(md5)

![[Pasted image 20251004151538.png]]  
  
*Explanation:* The `stats values(md5)` returns the MD5 hash(es) associated with the process creation that executed `3791.exe`. These hashes can be submitted to VirusTotal/ThreatMiner and used as IoCs.
![[Pasted image 20251004151716.png]]

##### Step 6: Summary & Cyber Kill Chain update  
- **Uploaded file identified:** `3791.exe` (confirmed by `stream:http` and `suricata` logs).  
- **Attacker source IP:** Identified in Suricata results.
- **Hashes extracted:** MD5 (and other hashes if present) retrieved from Sysmon process creation events — use these as strong indicators of compromise.  
- **Kill Chain:** This confirms the **Installation (Post-Exploitation)** phase — the adversary uploaded and executed a binary on the victim.

![[Pasted image 20251002164731.png]]

#### Task 5: Identify any C&C Related Activities on the Network

**Objective:**  
In the Command & Control (C2) phase of the Cyber Kill Chain we want to identify domains and DNS activity used for remote control. 

We will use Splunk’s `stream:dns` **sourcetype** to pivot on known suspicious IPs (e.g., **23.22.63.114**) and enumerate domain names that resolved to them.

---

##### Step 1: Search DNS answers for the suspicious IP using `stream:dns`  
**Query:**  
``SPL> index=botsv1 answer=23.22.63.114 sourcetype=stream:dns | stats values("name{}")``
![[Pasted image 20251004151817.png]]  
The `stream:dns` events return DNS answers that resolved to **23.22.63.114**. 
The `name{}` field lists the queried domain names that had that IP as an answer. 

**Findings:** `prankglassinebracket.jumpingcrab.com` 

**prankglassinebracket.jumpingcrab.com** stands out as malicious and probably was used by attackers to deface the web server.

---

##### Step 2: Confirm domain usage and activity (pivot to web / proxy / http logs)  
Using the domain(s) identified from DNS, pivot into `stream:http`, proxy logs, or web server logs to find traffic to/from those domains or any HTTP requests referencing them.  

**Example SPL to search web logs for the domain:**  
``SPL> index=botsv1 sourcetype=stream:http (host="192.168.250.70" OR dest="192.168.250.70") (uri="_prankglassinebracket.jumpingcrab.com_" OR host="prankglassinebracket.jumpingcrab.com")

*Inline note:* This helps confirm whether the domain was actively contacted or used in payloads/defacement.

---

##### Step 3: Correlate DNS queries with internal hosts (identify compromised clients)  
- Find which internal hosts requested the malicious domain to determine infected or compromised systems.  
**Example SPL:**  
``SPL> index=botsv1 sourcetype=stream:dns name="prankglassinebracket.jumpingcrab.com" | stats count by src dest _time

*Inline note:* `src` will show the internal resolver or host that requested the domain: **use this to pivot to endpoint logs and investigate further**.

---

##### Step 4: Enrich domain with OSINT and reputation checks  

Submit `prankglassinebracket.jumpingcrab.com` to VirusTotal, Passive DNS, ThreatIntel sources, and whois lookups to gather registration details, passive DNS history, and other IPs/domains in the same cluster.  
**Useful checks:** VirusTotal domain report, PassiveTotal / PassiveDNS, Censys/Shodan lookups, and WHOIS.

![[Pasted image 20251003084558.png]]  

![[Pasted image 20251003085051.png]]
The **WHOIS** output contains **strong operational indicators** (recent update + use of Afraid.org free DNS) that justify treating `jumpingcrab.com` and `prankglassinebracket.jumpingcrab.com` as **suspicious**.


---

##### Step 5: Update the Cyber Kill Chain & produce IoCs  
- Add the discovered domain(s) to the kill chain under **Command & Control**.  
- Produce a compact IOC list: domain(s), IP(s), timestamps of DNS answers, and internal hosts that queried them.

**Example IOC entry:**  
- Domain: `prankglassinebracket.jumpingcrab.com`  
- Resolved IP: `23.22.63.114`  
- Evidence: `stream:dns` events (see screenshot), correlated HTTP/Suricata/Proxy logs if present.
![[Pasted image 20251003083753.png]]
