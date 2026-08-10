Automated Phishing Analysis & Incident Response Pipeline

📖 Overview
This project is an automated Security Orchestration, Automation, and Response (SOAR) pipeline engineered to ingest, analyze, and escalate suspicious emails. Built as a collaborative cybersecurity initiative, the system leverages AI-driven analysis and threat intelligence to reduce alert fatigue, extract critical Indicators of Compromise (IoCs), and standardize incident triage for SOC environments.

(Suggested Image: A high-level screenshot of your entire n8n workflow zoomed out so the viewer can see the complete flow from left to right).

🛠️ Technology Stack
Automation Engine: n8n

Case Management / SIEM: TheHive & Cortex

Threat Intelligence: VirusTotal API

AI Triage: Google Gemini (LLM)

Protocols & Regex: IMAP, custom JavaScript parsing for observable extraction

 Architecture & Workflow
The pipeline operates completely autonomously, executing the following phases upon the receipt of a suspicious email:

1. Ingestion & Parsing
An IMAP trigger continuously monitors a dedicated security mailbox. Upon receiving a reported email, the pipeline uses custom JavaScript and Regex to cleanly parse the email headers and body, extracting the raw sender email address and any embedded URLs.

(Suggested Image: A screenshot showing the output of your n8n node where the clean email address is successfully extracted, similar to the value[0].address extraction we worked on).

2. Threat Intel & AI Triage
Extracted URLs are automatically routed to the VirusTotal API. The resulting malicious/suspicious detection scores, along with the extracted email artifacts, are sent to the Google Gemini model.

Gemini acts as an automated Level 1 SOC Analyst, utilizing a custom prompt to generate:

A concise Threat Risk Level (Low/Medium/High).

A brief analysis of the potential risk (e.g., domain typosquatting, payload delivery).

Actionable containment recommendations for the security team.

(Suggested Image: A screenshot of the Gemini node's output, showing the nicely formatted SOC triage report it generates).

3. Case Creation & Observable Enrichment
The pipeline deduplicates alerts dynamically and generates a unique incident case in TheHive. The Gemini triage report is appended to the case description.

Crucially, the extracted attacker email is added directly to TheHive as a mail Observable. This allows integrated tools like Cortex to immediately run automated analyzers against the threat actor's infrastructure.

(Suggested Image: A screenshot of the final, generated alert inside TheHive's dashboard, showing the dynamic Source Reference, the Gemini text, and the attached observables).

 TTPs & Defensive Mapping
This pipeline addresses the following adversary behaviors based on the MITRE ATT&CK framework:

Phishing (T1566): Automating the initial analysis of suspicious emails.

Malicious Link Execution (T1204.001): Pre-emptively scanning and analyzing user-reported URLs.

Indicator Removal on Host (T1070): Preserving the original sender artifacts dynamically before they can be obfuscated.
