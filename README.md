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
*   The pipeline utilizes dynamic identifier injection (Message-IDs/Execution Timestamps) to navigate SOAR deduplication constraints and prevent case redundancy.
*   Unique alerts are instantly provisioned in **TheHive**.
*   Extracted artifacts (e.g., attacker emails) are mapped as `mail` observables within the case, allowing **Cortex** to automatically execute active response analyzers against the adversary's infrastructure.

<p align="center">
  <img  src="https://github.com/user-attachments/assets/dbb70bd2-296b-43d3-9c2b-5ffa519c5e08" alt="3" />
  <br>
  <em>Alert creating with TheHaiv</em>
</p>

<p align="center">
  <img  src="https://github.com/user-attachments/assets/251d1b0d-a277-4e54-94cb-fb732090e7ce" alt="3" />
  <br>
  <em>Alert creating with TheHaiv 2</em>



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
