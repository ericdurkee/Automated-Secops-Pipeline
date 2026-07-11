# Automated SecOps Pipeline: Network Monitoring, SIEM Ingestion, and AI-Powered Threat Summarization

## Project Executive Summary
This project demonstrates the end-to-end engineering of an automated Security Operations (SecOps) pipeline. Built within a private homelab environment, the pipeline mirrors production network traffic at the hardware layer, processes it via Network Security Monitoring (NSM) tooling, ingests the telemetry into a SIEM, and orchestrates real-time threat intelligence lookups and generative AI analysis to deliver automated, dark-themed executive security digests.

* **The Problem:** Raw network alerts are noisy, causing alert fatigue and making rapid triage difficult for security teams.
* **The Solution:** A fully automated pipeline that captures network metadata, matches indicators against active threat intelligence databases, leverages LLMs to compile natural language executive summaries, and embeds a live SIEM dashboard visualization directly into a unified email digest.
* **Core Competencies Demonstrated:** Layer 2 Networking (SPAN/Port Mirroring), SIEM Engineering (Splunk Enterprise), Containerized Microservices (Docker), API Integration, Scripted Data Transformation (JavaScript), and Security Orchestration, Automation, and Response (SOAR).

---

## Step-by-Step Technical Walkthrough

### Step 1: Hardware Layer & Traffic Interception (TAP/SPAN)
To analyze network behavior without disrupting production traffic flows, packet capturing was established at the physical networking layer:
* **Infrastructure:** Intercepted active uplink traffic passing through an **eero 6+ router**.
* **Layer 2 Aggregation:** Deployed a managed **TP-Link TL-SG108E** switch. 
* **Traffic Mirroring:** Configured **Port Mirroring (SPAN)** inside the TP-Link switch GUI to replicate inbound/outbound gateway traffic and stream it directly to a dedicated network monitoring interface.

### Step 2: Sensor Deployment & SIEM Engineering
Once network packet streams were accessible, the collection and visualization infrastructure was built:
* **Network Security Monitoring (NSM):** Installed and configured **Zeek** to sniff the mirrored traffic interface, translating raw packets into structured protocol logs (`conn.log`, `dns.log`, `http.log`).
* **SIEM Centralization:** Installed a dedicated **Splunk Enterprise** server instance. Configured log forwarding to ingest raw Zeek telemetry into a custom index (`index=zeek`).
* **Dashboard Design:** Engineered a dark-mode Splunk dashboard to visualize protocol breakdowns, geometric top talkers, connection densities, and network alert intervals.

### Step 3: Containerized Automation & Microservices (Docker & n8n)
To orchestrate the data flow, **Docker** was deployed to host independent, containerized microservices:
* **SOAR Orchestration:** Hosted an instance of **n8n** to manage the conditional logic, timing controls, and API integration paths.
* **Headless Visual Capture:** Hosted a standalone **Browserless (Puppeteer/Headless Chrome)** container instance. This container securely authenticates into the Splunk web GUI using local credentials, waits for visual rendering, takes a high-definition screenshot (`dashboard_snap`), and outputs raw binary image data directly to n8n.

### Step 4: Threat Intelligence & AI Enrichment Pipeline
On a scheduled cron-interval trigger, the n8n automation engine executes the following logic:
1. **Splunk Search:** Authenticates with the Splunk Enterprise server and fetches the latest transaction events via a secure API query.
2. **Data Transformation:** Uses an n8n Code Node (JavaScript) to cleanse the array, calculate total metrics (Total Connections, Unique Destinations), and split individual event fields.
3. **Reputation Assessment:** Programmatically queries the **VirusTotal API** for each discovered destination IP to determine malicious activity counts.
4. **Generative AI Analytics:** Contextualizes current network activity by feeding connection baselines to the **Gemini AI API**, which returns a concise, natural language executive briefing.

### Step 5: Responsive Executive Alert Delivery
* Designed a responsive HTML email blueprint closely mirroring **Splunk's Corporate Dark Theme aesthetic** (`#111216`).
* Programmed inline Content-ID (`cid:dashboard_snap`) injection to cleanly embed the headless dashboard asset into the center of the email body, bypassing standard client-side image blocking.
* Formatted dynamic tables to cleanly switch between green success blocks ("No Malicious Network Threats Detected") and red critical alert blocks depending on real-time VirusTotal telemetry.

---

## 💻 Project Directory Layout

```text
automated-secops-pipeline/
├── .gitignore
├── README.md
├── docs/
│   ├── EmailZeek.png
│   ├── SplunkDashboard.png
│   ├── SplunkSearch.png
│   └── n8nWokflow.png
└── workflows/
    └── n8n-splunk-zeek-pipeline.json  <-- Core automation layout
