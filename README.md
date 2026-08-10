# 🛡️ Automated Phishing Analysis & Incident Response Pipeline

> **An AI-powered SOAR pipeline for automated phishing detection, threat intelligence enrichment, IOC extraction, and incident response.**

---

## 📖 Overview

This project is an automated **Security Orchestration, Automation, and Response (SOAR)** pipeline designed to ingest, analyze, enrich, and escalate suspicious emails with minimal human intervention.

Built as a collaborative cybersecurity initiative, the pipeline combines **n8n automation**, **Google Gemini AI**, **VirusTotal threat intelligence**, and **TheHive/Cortex** to reduce SOC alert fatigue, extract critical **Indicators of Compromise (IoCs)**, and standardize the initial incident-triage process.

### 🎯 Key Objectives

* 📥 Automatically ingest reported phishing emails.
* 🔍 Extract sender information and embedded URLs.
* 🧪 Enrich URLs using VirusTotal.
* 🤖 Use Google Gemini for automated Level 1 SOC triage.
* 🚨 Dynamically create and enrich cases in TheHive.
* 🔗 Add attacker email addresses as TheHive observables.
* 🧠 Enable Cortex analyzers for additional threat intelligence.
* ♻️ Deduplicate alerts to prevent unnecessary case creation.
* ⚡ Reduce manual SOC investigation and response time.

---

## 🏗️ Architecture

The overall workflow is orchestrated through **n8n**, connecting email ingestion, custom parsing, threat intelligence, AI analysis, and case management into a single automated pipeline.

```text
                    ┌─────────────────────┐
                    │   Security Mailbox  │
                    │       (IMAP)         │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    n8n IMAP Trigger │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Email Parsing & IOC  │
                    │ Extraction (JS/Regex)│
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
                    │                     │
                    ▼                     ▼
             ┌─────────────┐       ┌──────────────┐
             │  URLs / IoCs │       │ Sender Email │
             └──────┬──────┘       └───────┬──────┘
                    │                      │
                    ▼                      │
             ┌─────────────┐               │
             │ VirusTotal  │               │
             │ Threat Intel│               │
             └──────┬──────┘               │
                    │                      │
                    └──────────┬───────────┘
                               ▼
                    ┌─────────────────────┐
                    │   Google Gemini AI  │
                    │   L1 SOC Triage     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Deduplication & Case │
                    │     Generation       │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │       TheHive       │
                    │ Incident Management │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Cortex / Analyzers  │
                    │ IOC Enrichment      │
                    └─────────────────────┘
```

### 📸 Full Workflow

> **Suggested screenshot:** Add a zoomed-out screenshot of the complete n8n workflow showing the entire pipeline from left to right.

```text
![Complete n8n Workflow](images/n8n-workflow-overview.png)
```

---

# 🛠️ Technology Stack

| Component              | Technology             | Purpose                                 |
| ---------------------- | ---------------------- | --------------------------------------- |
| ⚙️ Automation          | **n8n**                | Workflow orchestration and automation   |
| 📧 Email Ingestion     | **IMAP**               | Monitoring the security mailbox         |
| 🧠 AI Triage           | **Google Gemini**      | Automated L1 SOC analysis               |
| 🔎 Threat Intelligence | **VirusTotal API**     | URL reputation and malware intelligence |
| 🚨 Case Management     | **TheHive**            | Incident and alert management           |
| 🧪 Threat Analysis     | **Cortex**             | Automated observable analysis           |
| 💻 Parsing             | **JavaScript / Regex** | Email and observable extraction         |
| 🗺️ Framework          | **MITRE ATT&CK**       | Adversary behavior mapping              |

---

# ⚙️ Workflow

## 1. 📥 Ingestion & Parsing

The pipeline continuously monitors a dedicated security mailbox using an **IMAP trigger**.

When a suspicious or user-reported email arrives, n8n automatically processes the message and extracts relevant artifacts.

### Extracted Artifacts

* Sender email address
* Email headers
* Email body
* Embedded URLs
* Other potentially relevant observables

Custom **JavaScript and Regex** logic is used to normalize the incoming email data and extract clean observables.

For example, the sender information is normalized from the original email structure into a clean email address:

```javascript
value[0].address
```

This allows the extracted address to be passed directly into downstream threat-analysis and case-management stages.

### 📸 Email Parsing Output

> **Suggested screenshot:** Show the n8n parsing node output with the successfully extracted sender email address.

```text
![Email Parsing Output](images/email-parsing-output.png)
```

---

# 2. 🔎 Threat Intelligence & AI Triage

After extraction, the identified URLs are automatically submitted to the **VirusTotal API** for reputation analysis.

The resulting threat-intelligence information is then combined with the extracted email artifacts and passed to **Google Gemini**.

Gemini functions as an automated **Level 1 SOC Analyst**, analyzing the available evidence and producing a structured triage report.

### 🤖 Gemini Triage Output

The AI generates:

* **Threat Risk Level**

  * 🟢 Low
  * 🟡 Medium
  * 🔴 High
* **Threat Analysis**

  * Potential phishing indicators
  * Domain typosquatting
  * Suspicious infrastructure
  * Malicious payload delivery
  * Other relevant indicators
* **Recommended Actions**

  * Containment recommendations
  * Investigation steps
  * Blocking recommendations
  * Additional analysis requirements

### Example Triage Structure

```text
Risk Level: HIGH

Analysis:
The submitted URL exhibits characteristics consistent with
a phishing campaign, including suspicious domain naming and
multiple malicious detections from threat intelligence sources.

Recommended Actions:
1. Block the identified domain.
2. Investigate affected users.
3. Search for additional messages containing the same IOC.
4. Analyze the sender infrastructure using Cortex.
5. Preserve the original email artifacts for further investigation.
```

### 📸 Gemini Output

> **Suggested screenshot:** Show the Google Gemini n8n node output containing the generated SOC triage report.

```text
![Gemini SOC Triage](images/gemini-triage-output.png)
```

---

# 3. 🚨 Case Creation & Observable Enrichment

Once the automated analysis is complete, the workflow performs alert deduplication before creating a new incident.

This prevents multiple reports containing the same indicators from unnecessarily generating duplicate cases.

The pipeline then dynamically creates a case in **TheHive**.

### TheHive Case Contains

* Dynamic source reference
* Original phishing artifacts
* Extracted sender email
* Extracted URLs
* VirusTotal results
* Gemini-generated threat assessment
* Recommended response actions
* Additional observables

The Gemini-generated triage report is appended to the case description, providing analysts with an immediately available initial assessment.

---

## 🔗 Observable Enrichment

One of the key features of the pipeline is the automatic creation of a **mail observable** from the extracted attacker email address.

```text
Suspicious Email
       │
       ▼
Extract Sender
       │
       ▼
Create Mail Observable
       │
       ▼
TheHive
       │
       ▼
Cortex Analyzer
       │
       ▼
Additional Threat Intelligence
```

This allows integrated analysis tools such as **Cortex** to automatically perform additional analysis against the identified infrastructure.

### 📸 TheHive Case

> **Suggested screenshot:** Show the final alert/case inside TheHive, including:
>
> * Dynamic source reference
> * Gemini triage report
> * Sender email observable
> * URL observables
> * Enrichment results

```text
![TheHive Case](images/thehive-case.png)
```

---

# 🧠 TTPs & MITRE ATT&CK Mapping

The pipeline addresses several adversary behaviors and defensive use cases mapped to the **MITRE ATT&CK** framework.

| Technique                    | ID            | Application in Pipeline                                               |
| ---------------------------- | ------------- | --------------------------------------------------------------------- |
| 🎣 Phishing                  | **T1566**     | Automates the analysis and triage of suspicious emails                |
| 🔗 Malicious Link            | **T1204.001** | Automatically extracts and analyzes URLs contained in reported emails |
| 🧹 Indicator Removal on Host | **T1070**     | Preserves original sender artifacts and observables for investigation |

### T1566 — Phishing

The pipeline automates the initial investigation of user-reported phishing emails, reducing the time required for manual triage.

### T1204.001 — Malicious Link

Embedded URLs are automatically extracted and submitted to VirusTotal for reputation and threat-intelligence analysis.

### T1070 — Indicator Removal on Host

Original email artifacts are preserved and dynamically added to the incident, helping maintain useful forensic indicators during investigation.

---

# 🔄 End-to-End Process

The complete automated process can be summarized as:

```text
1. User reports suspicious email
             │
             ▼
2. IMAP receives email
             │
             ▼
3. n8n parses email
             │
             ▼
4. Extract sender + URLs
             │
             ▼
5. Submit URLs to VirusTotal
             │
             ▼
6. Combine IoCs + VT results
             │
             ▼
7. Gemini performs L1 SOC triage
             │
             ▼
8. Determine threat severity
             │
             ▼
9. Deduplicate alert
             │
             ▼
10. Create TheHive case
             │
             ▼
11. Add email/URL observables
             │
             ▼
12. Cortex performs enrichment
             │
             ▼
13. SOC receives enriched incident
```

---

# 🚀 Key Benefits

### ⚡ Faster Incident Triage

Automates repetitive Level 1 SOC investigation tasks and provides analysts with an initial assessment immediately.

### 🤖 AI-Assisted Analysis

Google Gemini analyzes multiple data points and generates a concise, actionable threat assessment.

### 🔎 Automated Threat Intelligence

VirusTotal enrichment provides additional context around suspicious URLs.

### 🧩 Centralized Case Management

TheHive provides a centralized location for alerts, observables, analysis, and response actions.

### 🧪 Automated Observable Analysis

Extracted indicators can be automatically passed to Cortex for additional enrichment.

### ♻️ Alert Deduplication

Dynamic deduplication helps prevent repeated reports from creating unnecessary duplicate incidents.

### 📈 SOC Scalability

The architecture reduces repetitive manual work and allows analysts to focus on higher-value investigations.

---

# 🔐 Security Considerations

When deploying this pipeline in a production SOC environment:

* Store API keys and credentials securely.
* Use n8n credentials/environment variables rather than hardcoding secrets.
* Restrict access to the n8n instance.
* Apply appropriate authentication and authorization to TheHive and Cortex.
* Avoid sending sensitive email content to external AI services unless organizational policy permits it.
* Log workflow execution and failures for auditing.
* Validate and sanitize extracted observables before passing them to external services.
* Implement rate limiting for external APIs.
* Review AI-generated recommendations before executing high-impact containment actions.

---

# 📁 Suggested Repository Structure

```text
automated-phishing-analysis/
│
├── README.md
│
├── workflow/
│   └── phishing-analysis-workflow.json
│
├── scripts/
│   └── email-parser.js
│
├── prompts/
│   └── gemini-soc-triage.txt
│
├── images/
│   ├── n8n-workflow-overview.png
│   ├── email-parsing-output.png
│   ├── gemini-triage-output.png
│   └── thehive-case.png
│
└── docs/
    └── architecture.md
```

---

# 📸 Screenshots

## n8n Workflow

## Email Artifact Extraction

## Gemini AI Triage

## TheHive Incident

---

# 🧪 Example Use Case

### Scenario

A user reports a suspicious email containing a link to a potentially malicious website.

### Automated Response

1. 📧 The email arrives in the security mailbox.
2. ⚙️ n8n automatically triggers the workflow.
3. 🔍 The sender and embedded URLs are extracted.
4. 🧪 URLs are submitted to VirusTotal.
5. 🤖 Gemini evaluates the collected evidence.
6. 🔴 The email is classified as **High Risk**.
7. 🚨 A case is automatically created in TheHive.
8. 🔗 Sender and URL observables are attached to the case.
9. 🧠 Cortex performs additional observable enrichment.
10. 👨‍💻 The SOC analyst receives a structured incident ready for investigation.

---

# 🎯 Project Goals

This project demonstrates how **SOAR automation, threat intelligence, and generative AI** can be combined to build a practical automated phishing-response workflow.

The ultimate goal is to transform a raw suspicious email into an **enriched, prioritized, and actionable security incident** with minimal manual intervention.

---

# 📌 Future Improvements

Potential enhancements include:

* [ ] Automated malicious-domain blocking
* [ ] Integration with endpoint detection platforms
* [ ] Automatic IOC blocking through firewall/DNS solutions
* [ ] Slack/Teams SOC notifications
* [ ] Automated incident severity scoring
* [ ] Attachment malware analysis
* [ ] YARA/Sigma-based detection enrichment
* [ ] Automated threat-intelligence correlation
* [ ] Analyst feedback loop for improving AI triage
* [ ] Expanded MITRE ATT&CK mapping
* [ ] Automated incident closure for confirmed false positives

---

# 👥 Project

**Automated Phishing Analysis & Incident Response Pipeline**

Built as a collaborative cybersecurity automation project demonstrating the integration of:

**n8n + VirusTotal + Google Gemini + TheHive + Cortex**

> ⚠️ **Disclaimer:** This project is intended for cybersecurity research, SOC automation, and authorized defensive security operations. Ensure that all integrations, email analysis, and automated response actions are performed only within systems and environments you are authorized to monitor and control.

