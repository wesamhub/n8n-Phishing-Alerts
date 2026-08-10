#  Automated Phishing Analysis with n8n & virustotal

##  Executive Summary
This project showcases an automated Security Orchestration, Automation, and Response (SOAR) architecture designed to minimize SOC alert fatigue and accelerate Level 1 triage. Co-architected and developed collaboratively, this pipeline autonomously ingests suspicious emails, leverages AI and external threat intelligence to analyze artifacts, and provisions enriched case files in a case management environment. 

This initiative demonstrates a professional approach to threat detection, reducing Mean Time to Detect (MTTD) and standardizing incident response workflows.

<p align="center">
  <img  src="https://github.com/user-attachments/assets/f03d90e4-aa05-43d7-80e5-375ca218b7a4" alt="1" />
  <br>
  <em>n8n workflow</em>
</p>


##  Core Architecture & Workflow

### 1. Automated Ingestion & Artifact Parsing
*   An IMAP sensor actively monitors a dedicated security mailbox.
*   Upon receipt, custom JavaScript and Regex parse the email headers and body to extract immutable artifacts, specifically targeting the raw sender address and embedded URLs before they can be obfuscated by the attacker.

  <p align="center">
  <img src="https://github.com/user-attachments/assets/81ca9bbb-0a21-447f-a364-9dfedc968075" alt="2" />
  <br>
  <em>Email Trigger</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/04c05045-9176-42d3-8da3-b1f26cd6c1f9" alt="3" />
  <br>
  <em>script for extracting emails</em>
</p>



### 2. Threat Intel & AI-Driven Triage
*   Extracted URLs are routed to the **VirusTotal API** for malicious signature and behavior detection.
*   The raw telemetry is forwarded to **Google Gemini (LLM)**, acting as a virtual Level 1 Analyst. Gemini is prompted with a strict SOC persona to generate a structured Triage Report containing:
    *   **Threat Risk Level** (Low/Medium/High)
    *   **Risk Analysis** 
    *   **Containment Recommendations**



<p align="center">
  <img  src="https://github.com/user-attachments/assets/d61a32dd-2230-4f14-867a-3ebc906e88a4" alt="3" />
  <br>
  <em>POST request</em>
</p>


<p align="center">
  <img  src="https://github.com/user-attachments/assets/7c8121cc-09ff-4201-89f4-c53a2005396b" alt="3" />
  <br>
  <em>GET request</em>
</p>



### 3. Observable Enrichment
*   The pipeline utilizes dynamic identifier injection to navigate SOAR deduplication constraints and prevent case redundancy.
*   Unique alerts are instantly provisioned in **TheHive**.
*   Extracted artifacts are mapped as `mail` and `url` observables within the case.
  
<p align="center">
  <img  src="https://github.com/user-attachments/assets/dbb70bd2-296b-43d3-9c2b-5ffa519c5e08" alt="3" />
  <br>
  <em>Alert creating with TheHive</em>
</p>

<p align="center">
  <img  src="https://github.com/user-attachments/assets/251d1b0d-a277-4e54-94cb-fb732090e7ce" alt="3" />
  <br>
  <em>Alert creating with TheHive 2</em>


# 🕵️ End-to-End Execution Demonstration

To validate the architecture, a simulated phishing email was delivered to the monitored inbox. Below is the visual lifecycle of the pipeline autonomously handling the incident.

### 1. Initial Ingestion
The raw phishing attempt as received by the dedicated security mailbox.

<p align="center">
  <img src="https://github.com/user-attachments/assets/0434cd06-e91d-4492-b011-6a55dc9fc451" alt="Simulated Phishing Email" />
  <br>
  <em>Suspicious email containing malicious artifacts delivered to the inbox.</em>
</p>

### 2. SOAR Pipeline Execution
The n8n automation engine actively parsing the payload, extracting observables, and querying both the VirusTotal API and Google Gemini.


<p align="center">
  <img src="https://github.com/user-attachments/assets/2f55f554-d527-4bed-8af4-cc6d7a50af92" alt="n8n Execution Logs" />
  <br>
  <em>Successful end-to-end execution of the n8n workflow.</em>
</p>

### 3. Automated Case Provisioning
The final, enriched alert presented to the SOC team, complete with AI-driven triage recommendations and cleanly mapped observables ready for active response.

<p align="center">
  <img src="https://github.com/user-attachments/assets/271fe920-f9b6-44ec-9c15-b3cb29c7fcd5" alt="TheHive Alert 1" />
  <br>
  <em>Dynamic case creation in TheHive displaying the Gemini triage report and extracted observables.</em>
</p> 

<p align="center">
  <img src="https://github.com/user-attachments/assets/1615fde3-1794-4ae9-96db-23bc501f87c2" alt="TheHive Alert 2" />
  <br>
  <em>Dynamic case creation in TheHive displaying the Gemini triage report and extracted observables.</em>
</p 

<p align="center">
  <img src="https://github.com/user-attachments/assets/897e68b0-74d6-4113-a193-14e64c0b3f74" alt="TheHive Alert" />
  <br>
  <em>Dynamic case creation in TheHive displaying the Gemini triage report and extracted observables.</em>
</p

##  Defensive Mapping (MITRE ATT&CK)
This architecture actively defends against the following adversary techniques:
*   **Initial Access [T1566]:** Phishing - Automating the detection and initial triage of suspicious email delivery.
*   **Execution [T1204.001]:** Malicious Link - Pre-emptively scanning user-reported URLs to prevent payload execution.

##  Technology Stack
*   **Automation/SOAR:** n8n
*   **Case Management:** TheHive 5
*   **Threat Intel / Enrichment:** Cortex, VirusTotal API
*   **AI Analysis:** Google Gemini
*   **Data Parsing:** Regex, custom JavaScript
