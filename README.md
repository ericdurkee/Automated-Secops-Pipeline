Automated SecOps Pipeline: Network Monitoring, SIEM Ingestion, and AI-Powered Threat Summarization
📋 Project Executive Summary
This project demonstrates the design and implementation of an automated Security Operations (SecOps) pipeline built within a private network environment. By mirroring live network traffic at the hardware layer, capturing it via network security monitoring (NSM) tooling, and centralizing it within a SIEM, the pipeline successfully orchestrates real-time threat intelligence lookup, generative AI contextual analysis, and executive dashboard delivery.

Objective: Eliminate alert fatigue by converting raw network packet data into high-fidelity, visually enriched, and AI-summarized executive threat briefings.

Core Competencies Demonstrated: Network Engineering (L2 Switching/Port Mirroring), SIEM Engineering, Containerized Microservices (Docker), API Integration, Scripted Data Transformation (JavaScript), and SecOps Automation.

🛠️ System Architecture & Data Flow
[ eero 6+ Router ] <---> [ TP-Link TL-SG108E Switch ] (Port Mirroring)
                                    |
                                    v (SPAN/Mirror Traffic)
                             [ Zeek NSM Sensor ] 
                                    |
                                    v (Raw Logs)
                            [ Splunk Enterprise ] (index=zeek)
                                    |
                                    v (n8n Scheduled Query Webhook)
                        [ n8n Automation Engine (Docker) ]
                                    |
            +-----------------------+-----------------------+
            |                       |                       |
            v                       v                       v
 [ VirusTotal API ]          [ Browserless Docker ]   [ Gemini AI API ]
(IP Threat Intelligence)    (Automated Dashboard Snapshot)  (Executive Briefing)
            |                       |                       |
            +-----------------------+-----------------------+
                                    |
                                    v (Unified JSON & Binary Compile)
                         [ Secure Executive Email ] (Splunk Dark Theme UI)
💻 Technical Implementation Details
1. Hardware & Network Layer (Traffic Capture)
Infrastructure: Intercepted active uplink traffic passing through an eero 6+ router.

Layer 2 Aggregation: Implemented a managed TP-Link TL-SG108E switch. Configured Port Mirroring (SPAN) via the TP-Link switch GUI to replicate inbound/outbound target gateway traffic and stream it directly to a dedicated monitoring interface without disrupting production network flows.

2. Network Security Monitoring & SIEM Ingestion
Sensor Deployment: Configured Zeek (formerly Bro) to capture raw packet data on the mirrored interface, continuously generating structural metadata logs (conn.log, dns.log, http.log).

SIEM Centralization: Staged a Splunk Enterprise Server. Built a custom ingestion pipeline filtering connection activities into a dedicated security repository (index=zeek). Designed an active visualization dashboard tracking geometric top talkers, connection densities, and metric baseline intervals.

3. Containerized SOAR Orchestration Pipeline (n8n Workflow)
The brain of the infrastructure is a containerized n8n orchestration platform hosted via Docker. The asynchronous workflow performs the following data operations:

Ingestion: Triggers on a scheduled cron-interval, issuing an authenticated API query to pull the latest transaction events from Splunk.

Data Cleansing & Splitting: Utilizes n8n's code node architecture to parse raw indices and fork concurrent payload tracking items.

Threat Intel Enrichment: Automatically queries the VirusTotal API for each destination IP to analyze network reputation score configurations.

Headless Visual Capture: Commands a standalone Browserless (Puppeteer/Headless Chrome) Docker container to authenticate securely with the Splunk server, render the graphical interface components, and output a high-definition binary screenshot (dashboard_snap).

Generative AI Analytics: Feeds connection baselines into the Gemini AI API, returning a natural language threat intelligence briefing.

4. Dynamic HTML Output Generation
Converted the collected telemetry data into an elite, responsive HTML email payload stylized specifically around Splunk's Corporate Dark UI aesthetic (#111216).

Map logical data sets via advanced inline Content-ID (cid:dashboard_snap) injection to embed the headless dashboard captures natively inside the email body, preventing security block rules from filtering standard visual assets.
