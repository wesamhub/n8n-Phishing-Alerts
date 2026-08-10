### 🛡️ Automated Phishing Analysis & SOC Incident Response Pipeline

# 📖 Executive Summary
This project showcases an automated Security Orchestration, Automation, and Response (SOAR) architecture designed to minimize SOC alert fatigue and accelerate Level 1 triage. Co-architected and developed collaboratively, this pipeline autonomously ingests suspicious emails, leverages AI and external threat intelligence to analyze artifacts, and provisions enriched case files in a case management environment. 

This initiative demonstrates a professional approach to threat detection, reducing Mean Time to Detect (MTTD) and standardizing incident response workflows.

# ⚙️ Core Architecture & Workflow

## 1. Automated Ingestion & Artifact Parsing
*   An IMAP sensor actively monitors a dedicated security mailbox.
*   Upon receipt, custom JavaScript and Regex parse the email headers and body to extract immutable artifacts, specifically targeting the raw sender address and embedded URLs before they can be obfuscated by the attacker.

## 2. Threat Intel & AI-Driven Triage
*   Extracted URLs are routed to the **VirusTotal API** for malicious signature and behavior detection.
*   The raw telemetry is forwarded to **Google Gemini (LLM)**, acting as a virtual Level 1 Analyst. Gemini is prompted with a strict SOC persona to generate a structured Triage Report containing:
    *   **Threat Risk Level** (Low/Medium/High)
    *   **Risk Analysis** (e.g., Domain Typosquatting, Credential Harvesting)
    *   **Containment Recommendations**

## 3. Case Provisioning & Observable Enrichment
*   The pipeline utilizes dynamic identifier injection (Message-IDs/Execution Timestamps) to navigate SOAR deduplication constraints and prevent case redundancy.
*   Unique alerts are instantly provisioned in **TheHive**.
*   Extracted artifacts (e.g., attacker emails) are mapped as `mail` observables within the case, allowing **Cortex** to automatically execute active response analyzers against the adversary's infrastructure.

# 🧠 Defensive Mapping (MITRE ATT&CK)
This architecture actively defends against the following adversary techniques:
*   **Initial Access [T1566]:** Phishing - Automating the detection and initial triage of suspicious email delivery.
*   **Execution [T1204.001]:** Malicious Link - Pre-emptively scanning user-reported URLs to prevent payload execution.

# 🛠️ Technology Stack
*   **Automation/SOAR:** n8n
*   **Case Management:** TheHive 5
*   **Threat Intel / Enrichment:** Cortex, VirusTotal API
*   **AI Analysis:** Google Gemini
*   **Data Parsing:** Regex, custom JavaScript

# 🤝 Collaborative Development
This pipeline was designed and engineered as a collaborative team effort to replicate enterprise-grade SOC operations. 

*   **Wesam Hamdan:** Pipeline Architecture, AI prompt engineering, Regex data extraction, and API integration.
*   **[Insert Team Member Name]:** [Insert role, e.g., SIEM provisioning, Threat Intel routing, Quality Assurance.]

# 💡 Key Learnings & Future Enhancements
*   **Data Sanitization:** Overcame challenges with nested JSON payloads from email headers to reliably extract clean observables for automated routing.
*   **Future Roadmap:** Future iterations will include automated blocklist generation for edge firewalls and secure email gateways based on Cortex analyzer outputs.

---
