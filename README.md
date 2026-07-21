<div align="center">

# ThreatLens

### Automated Threat Intelligence & Attack Surface Mapping Platform

**Author:** Ritvik Indupuri &nbsp;|&nbsp; **Date:** February 2026

[![Live Demo](https://img.shields.io/badge/Live_Demo-threatintellig.netlify.app-00C853?style=for-the-badge&logo=netlify&logoColor=white)](https://threatintellig.netlify.app/)
[![AI Powered](https://img.shields.io/badge/AI_Powered-Gemini_3_Flash-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://ai.google.dev/)
[![Scraping](https://img.shields.io/badge/Web_Scraping-Firecrawl_API-FF6D00?style=for-the-badge&logo=firefox&logoColor=white)](https://firecrawl.dev/)
[![Search Engine](https://img.shields.io/badge/Search-Elasticsearch_8.x-005571?style=for-the-badge&logo=elasticsearch&logoColor=white)](https://elastic.co/)

---

</div>

## 📌 Executive Overview

**ThreatLens** is an enterprise-grade cybersecurity platform designed to automate threat intelligence gathering, attack surface mapping, and vulnerability correlation for web domains. 

By combining high-speed automated web crawling via **Firecrawl** with artificial intelligence powered by **Google Gemini 3 Flash Preview**, ThreatLens conducts comprehensive, non-invasive security assessments — surfacing exposed endpoints, technology fingerprints, security header gaps, real-time WHOIS/GeoIP metadata, and live NIST NVD CVE vulnerabilities.

The platform includes an **AI Domain Policy Agent** to prevent unauthorized scanning, an interactive **AI Security Analyst Chatbot** for deep-dive investigation, **Elasticsearch (⌘K)** enterprise search, and automated scheduled scans.

---

## 🌐 Live Demo

Explore the live application: **[https://threatintellig.netlify.app/](https://threatintellig.netlify.app/)**

---

## ✨ Key Features

- **Automated Reconnaissance & Crawling** — One-click deep domain scanning via Firecrawl API (crawling & site mapping up to 500 endpoints).
- **AI Policy Gate & Safety Agent** — Gemini 3 Flash Preview-powered policy evaluation that auto-approves legitimate sites, blocks sensitive targets (`.mil`, critical infrastructure, core banking), and flags ambiguous domains for review.
- **Parallel Analysis Engines** — Concurrent execution of Endpoint Discovery, Technology Fingerprinting (50+ stacks), Security Headers audit, and Multi-Source Enrichment (RDAP WHOIS, `ip-api.com` GeoIP/ASN, SSL/TLS certificates).
- **Real CVE Vulnerability Detection** — Automated matching of detected technology stacks against the NIST NVD 2.0 database to surface published CVEs, CVSS scores, and official advisories.
- **Findings Engine & Risk Scoring** — Intelligent correlation of misconfigurations into a weighted composite 0–100 risk score (*Critical: 25pts*, *High: 15pts*, *Medium: 8pts*, *Low: 3pts*, *Info: 1pt*).
- **Interactive AI Analyst Chatbot** — Embedded context-aware assistant powered by Gemini 3 Flash offering three investigation modes (*Attack Surface*, *Findings*, *Raw Data*).
- **Elasticsearch Enterprise Search (⌘K)** — Automatic background sync to Elastic Cloud across indices (`threatlens-scans`, `threatlens-findings`, `threatlens-audit`) for sub-second command-palette search and Kibana threat analytics.
- **PDF & AI Threat Reports** — Professional executive PDF report generation (`jsPDF`) with custom branding, cover page, severity breakdown, confidential watermarks, and AI remediation roadmaps.
- **Scan Comparison & Delta Analysis** — Side-by-side comparison between historical scans to identify attack surface drift, new exposures, and risk score deltas.
- **Automated Recurring Scans** — Configurable daily, weekly, biweekly, or monthly scan schedules driven by a backend cron runner.
- **REST API & Programmatic Access** — Full REST API gateway with SHA-256 hashed API keys for integrating ThreatLens into CI/CD pipelines, Python scripts, or SIEM systems.

---

## 🏗️ System Architecture

<div align="center">
  <img src="https://i.imgur.com/htEgLvc.png" alt="ThreatLens System Architecture Diagram" width="100%" />
  <br/><br/>
  <sub><strong>Figure 1: ThreatLens End-to-End System Architecture & Data Flow</strong></sub>
</div>

<br/>

### End-to-End Execution Flow

The system operates across **6 sequential and parallel stages**, orchestrated via Deno Edge Functions and Lovable Cloud (Supabase):

```
Target Domain ➔ AI Policy Gate ➔ Reconnaissance ➔ Parse & Normalize ➔ Parallel Analysis ➔ Findings & Risk ➔ Intelligence & Storage ➔ Actionable Outputs
```

#### 1. Domain Submission & AI Policy Gate
When a user submits a target domain, it passes through the **AI Policy Gate** (`evaluate-domain` Edge Function). Powered by **Gemini 3 Flash Preview**, the policy agent evaluates safety policy and usage rules:
- **Allowed**: Public websites, SaaS platforms, corporate portals, educational domains proceed directly to reconnaissance.
- **Blocked**: Government military (`.mil`), critical infrastructure, and core banking portals are automatically blocked.
- **Review**: Ambiguous domains are flagged for manual review on the Policies dashboard.

#### 2. Stage 1 — Reconnaissance (Firecrawl)
Upon domain approval, the `firecrawl-scan` Edge Function initiates automated reconnaissance using the Firecrawl API:
- **Firecrawl Scrape**: Crawls the primary landing page, extracting full HTML DOM, rendered markdown, and external links.
- **Firecrawl Map**: Discovers subpages, linked paths, and sitemap endpoints (up to 200–500 URLs).

#### 3. Stage 2 — Parse & Normalize
Raw HTML and discovered link vectors are processed by the normalization engine:
- Cleans, deduplicates, and normalizes all discovered URLs.
- Extracts inline/external JavaScript bundles (`.js`, `.mjs`), HTML form input vectors, external third-party script sources, and meta HTTP headers.

#### 4. Stage 3 — Parallel Analysis Engines
The normalized data is passed concurrently into four specialized analysis modules:
- 🔗 **Endpoint Discovery**: Extracts API endpoints, query parameters, hidden subpaths, admin portals, and sensitive routes.
- 🛠️ **Technology Detection**: Fingerprints frameworks, CMS platforms, web servers, and client libraries (e.g., React, Next.js, WordPress, Cloudflare, jQuery).
- 🛡️ **Security Headers**: Evaluates HTTP response and meta security headers including Content-Security-Policy (CSP), HTTP Strict Transport Security (HSTS), X-Frame-Options, CORS policies, and MIME options.
- 🌐 **Enrichment & Real CVE Lookup**:
  - **WHOIS & RDAP**: Live domain registration details, creation/expiry dates, registrar data.
  - **IP & GeoIP Info**: Real-time hosting provider, ASN, ISP, and geographic country lookups via `ip-api.com`.
  - **NIST NVD CVE Matching**: Fingerprinted technologies are queried against the NIST NVD 2.0 CVE database via the `cve-lookup` Edge Function to surface published CVE IDs, CVSS scores, and official vendor advisories.

#### 5. Stage 4 — Findings Engine & Risk Scoring
- **Findings Engine**: Correlates results across all analysis engines to flag security misconfigurations, XSS input risks, missing security controls, supply chain vulnerabilities, and active CVEs.
- **Risk Scoring**: Calculates a composite 0–100 risk score based on weighted severity metrics (*Critical: 25pts*, *High: 15pts*, *Medium: 8pts*, *Low: 3pts*, *Info: 1pt*).

#### 6. Stage 5 — Intelligence & Storage Layer
- **Supabase (PostgreSQL)**: Primary relational datastore storing persistent scan metadata (`scans`), findings (`findings`), domain policies (`domain_policies`), audit trails (`scan_audit_log`), API keys (`api_keys`), and quotas (`scan_quotas`). Protected by Row Level Security (RLS).
- **Elasticsearch (Elastic Cloud)**: Completed scans, findings, and audit logs are automatically synced via the `elasticsearch-sync` Edge Function to Elastic Cloud across three dedicated indices (`threatlens-scans`, `threatlens-findings`, `threatlens-audit`).

#### 7. Stage 6 — Actionable Outputs & User Interface
- 📊 **Interactive Dashboard**: Visual breakdown of attack surface map, risk scores, discovered assets, and severity distribution.
- 💬 **AI Chat Insights**: Context-aware AI Security Analyst (`AiChatPanel`) using Gemini 3 Flash Preview for deep-dive investigation into endpoints, dependencies, and raw crawl data.
- 📄 **PDF & JSON Reports**: Downloadable, branded executive reports (`pdf-export.ts`) and AI-generated threat briefings (`analyze-threats`).
- 📈 **Insights & Trends**: Historical scan comparison (`Compare.tsx`) for delta analysis of risk posture over time.
- 🔍 **Global Search (⌘K)**: Sub-second command palette search powered by `elasticsearch-search` with fuzzy query matching and category aggregations.

---

## 💻 Tech Stack

| Layer | Technology | Description |
|---|---|---|
| **Frontend Framework** | React 18 + TypeScript + Vite | Single-page web application architecture |
| **Styling & UI** | Tailwind CSS + shadcn/ui + Framer Motion | Modern dark-mode UI with glassmorphism & micro-animations |
| **State & Fetching** | TanStack React Query v5 | Efficient server state management, caching, and auto-refetching |
| **Routing** | React Router v6 | Client-side routing with protected auth guards |
| **Backend Compute** | Lovable Cloud (Supabase) | Serverless Deno Edge Functions |
| **Database** | PostgreSQL | Relational database with Row Level Security (RLS) policies |
| **Authentication** | Google OAuth | Managed identity provider via Supabase Auth |
| **AI Inference** | Google Gemini 3 Flash Preview | Policy evaluation, threat report generation, and AI Analyst chat |
| **Web Reconnaissance** | Firecrawl API | High-speed DOM scraping & endpoint mapping |
| **CVE Intelligence** | NIST NVD 2.0 API | Real CVE vulnerability lookups for detected tech stacks |
| **Enrichment Services** | RDAP WHOIS + `ip-api.com` | Real domain WHOIS, ASN, ISP, and GeoIP lookup |
| **Search & Analytics** | Elastic Cloud (Elasticsearch + Kibana) | Enterprise full-text search & Kibana analytics dashboards |
| **PDF Generation** | jsPDF + AutoTable | Client-side executive report synthesis |
| **Data Visualization** | Recharts | Interactive risk scores, severity distribution & exposure charts |

---

## 🔑 REST API (Programmatic Access)

ThreatLens provides a full REST API for triggering scans, querying results, and integrating threat intelligence into CI/CD pipelines, SIEMs, or custom scripts.

### Authentication
API requests require an API key passed in the `x-api-key` header. Generate API keys from **Settings ➔ API Keys** in the web interface. All keys are hashed with SHA-256 before storage.

### Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/functions/v1/api-gateway/scan` | Initiate a new security scan for a target domain |
| `GET` | `/functions/v1/api-gateway/scan/:id` | Fetch scan details, risk score, technologies, and metadata |
| `GET` | `/functions/v1/api-gateway/scan/:id/findings` | Retrieve all detected findings and CVEs for a scan |
| `GET` | `/functions/v1/api-gateway/scans` | List recent scans for the authenticated user (`?limit=20`) |

### cURL Examples

```bash
# 1. Trigger a domain scan
curl -X POST https://<your-supabase-url>/functions/v1/api-gateway/scan \
  -H "Content-Type: application/json" \
  -H "x-api-key: tl_your_api_key_here" \
  -d '{"domain": "example.com"}'

# 2. Retrieve scan status & risk score
curl -H "x-api-key: tl_your_api_key_here" \
  https://<your-supabase-url>/functions/v1/api-gateway/scan/SCAN_ID_HERE

# 3. Fetch detected vulnerabilities & CVEs
curl -H "x-api-key: tl_your_api_key_here" \
  https://<your-supabase-url>/functions/v1/api-gateway/scan/SCAN_ID_HERE/findings
```

---

## ⚡ Setup & Local Development

### Prerequisites
- **Node.js**: v18.0.0 or higher (or **Bun**)
- **npm** / **yarn** / **bun**
- A **Lovable Cloud** or **Supabase** project
- **Firecrawl API Key** ([https://firecrawl.dev](https://firecrawl.dev))

### Installation Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/ritvikindupuri/Threatlens.git
   cd Threatlens
   ```

2. **Install Dependencies**
   ```bash
   npm install
   ```

3. **Configure Environment Variables**
   Create a `.env` file in the project root:
   ```env
   VITE_SUPABASE_URL=https://your-project.supabase.co
   VITE_SUPABASE_PUBLISHABLE_KEY=your-supabase-anon-key
   VITE_SUPABASE_PROJECT_ID=your-project-id
   ```

4. **Configure Edge Function Secrets**
   Set the following secrets in your Supabase / Lovable Cloud project settings:
   - `FIRECRAWL_API_KEY`: Firecrawl API authentication key
   - `LOVABLE_API_KEY`: API key for Gemini 3 Flash Preview AI gateway

5. **Start the Development Server**
   ```bash
   npm run dev
   ```
   Open `http://localhost:5173` in your browser.

6. **Run Tests & Linting**
   ```bash
   npm run test
   npm run lint
   ```

---

## 📚 Technical Documentation

For an in-depth breakdown of database schema definitions, Row Level Security policies, Edge Function implementations, and security architectural decisions, see **[TECHNICAL_DOCS.md](./TECHNICAL_DOCS.md)**.

---

<div align="center">
  <sub>Built by <strong>Ritvik Indupuri</strong> • 2026</sub>
</div>
