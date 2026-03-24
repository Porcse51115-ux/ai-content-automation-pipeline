# 🤖 AI Content Automation Pipeline
### End-to-End API Integration & Workflow Automation Project

![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)
![Platform](https://img.shields.io/badge/Platform-n8n-orange)
![APIs](https://img.shields.io/badge/APIs%20Integrated-6-blue)
![Type](https://img.shields.io/badge/Type-Portfolio%20Project-green)
![Date](https://img.shields.io/badge/Date-March%202026-lightgrey)

---

## 📋 Project Overview

This project involved designing, configuring, and deploying a fully automated AI-powered content production pipeline using **n8n** workflow automation software. The pipeline integrates six external APIs and services to automatically:

- Generate viral video scripts using AI
- Produce AI voiceovers from text
- Create AI-generated video clips
- Assemble final short-form videos with subtitles
- Manage content through a cloud database
- Store all assets in cloud object storage

Everything runs on an automated schedule — no manual intervention required.

---

## 🛠️ Technology Stack

| Service | Purpose | IT Concept |
|---|---|---|
| **n8n** | Workflow automation engine | Process automation, ETL pipelines |
| **OpenAI GPT-4o-mini** | AI script & caption generation | AI/ML API integration |
| **ElevenLabs** | Text-to-speech voiceover | Audio API, REST integration |
| **Replicate** | AI video clip generation | GPU compute APIs, async jobs |
| **Shotstack** | Video assembly & rendering | Media processing APIs |
| **Cloudflare R2** | Cloud file storage | S3-compatible object storage |
| **Airtable** | Database & content management | REST API, database design |

---

## ⚙️ Pipeline Architecture

The pipeline operates in 4 automated phases:

```
┌─────────────────────────────────────────────────────────┐
│  TRIGGER: Every 8 Hours (Scheduled)                     │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 1 — Content Generation                           │
│  • Fetch topic from Airtable (Status = "New")           │
│  • Generate 80-word viral script via GPT-4o-mini        │
│  • Create 4 visual prompts for video clips              │
│  • Update Airtable status to "In Progress"              │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 2 — Video Production                             │
│  • Generate 4 AI video clips via Replicate              │
│  • Create voiceover audio via ElevenLabs                │
│  • Transcribe audio → generate SRT subtitle file        │
│  • Assemble final video via Shotstack                   │
│  • Upload all assets to Cloudflare R2                   │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 3 — Storage & Tracking                           │
│  • Store final video URL in Airtable                    │
│  • Update status to "Video Complete"                    │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  PHASE 4 — Ready to Post Notification                   │
│  • Mark record as "Ready to Post" in Airtable           │
│  • Manual review and publishing to social platforms     │
└─────────────────────────────────────────────────────────┘
```

---

## 💡 Technical Skills Demonstrated

### 🔐 API Integration & Authentication
- Connected 6 external APIs using multiple authentication methods
- **HTTP Header Auth** — ElevenLabs, Replicate, Shotstack, Airtable
- **Bearer Token Auth** — OpenAI, Replicate
- **OAuth2** — Google Sheets (attempted, diagnosed failure)
- **AWS Signature v4** — Cloudflare R2 via S3-compatible API
- Diagnosed and resolved authentication errors across multiple services

### ☁️ Cloud Storage Configuration
- Set up **Cloudflare R2** object storage (S3-compatible)
- Configured API tokens with correct permission scopes
- Distinguished between public URLs and private API endpoints
- Configured S3-compatible credentials in third-party applications
- Managed bucket naming, access policies, and custom endpoints

### 🗄️ Database Design & Management
- Designed table structure in **Airtable** with multiple related tables
- Created custom field types: single line text, long text, single select, URL, date, number
- Defined a status workflow: `New` → `In Progress` → `Video Complete` → `Ready to Post`
- Queried records using filter formulas via REST API
- Updated records programmatically using `PATCH` requests

### 📄 JSON & File Manipulation
- Opened, read, and understood complex **82-node JSON** workflow files
- Performed targeted Find & Replace operations across large JSON files
- Modified node parameters, typeVersions, and API endpoints within JSON
- Imported and exported workflow configurations between environments

### 🔧 Troubleshooting & Debugging
- Diagnosed credential mismatch errors across 6 different services
- Identified API response format differences between native nodes and HTTP requests
- Resolved node `typeVersion` compatibility issues between n8n versions
- Traced cascading errors through multi-step workflow chains
- Switched authentication strategies when primary methods failed
- Used direct HTTP requests to bypass buggy native integration nodes

### ⚡ Workflow Automation
- Built and configured an **82-node** automated workflow
- Configured trigger-based execution (scheduled every 8 hours)
- Set up conditional logic nodes (IF statements, fallback handling)
- Managed data flow between nodes using expressions and variable references
- Simplified a complex workflow by removing unnecessary components

---

## 🚧 Challenges & Solutions

### Challenge 1 — OAuth2 API Not Enabled
**Problem:** Google Sheets OAuth2 kept failing with "error fetching options"  
**Root Cause:** The Google Sheets API was not enabled in Google Cloud Console — only the YouTube Data API had been activated  
**Solution:** Navigated to Google Cloud Console → APIs & Services → Library → enabled Google Sheets API  
**Lesson:** OAuth2 credentials and API enablement are separate steps in Google Cloud — both must be configured

---

### Challenge 2 — API Response Format Mismatch
**Problem:** `Prepare Data` node threw `No new items found in Airtable` even though records existed  
**Root Cause:** Switched from native Airtable node to HTTP Request node changed the JSON response structure — native node returns flat records, HTTP API returns `{ records: [{ id, fields: {...} }] }`  
**Solution:** Modified the JavaScript code node to extract data from the `records[0].fields` path instead of the root object  
**Lesson:** Always verify the actual API response structure — native integrations often abstract/flatten the raw API response

---

### Challenge 3 — Node TypeVersion Conflicts
**Problem:** Workflow imported with error `Cannot read properties of undefined (reading 'execute')`  
**Root Cause:** When nodes were converted from one type to another in the JSON file, they inherited the wrong `typeVersion` number from the original node type  
**Solution:** Audited all modified nodes and corrected `typeVersion` values to match the target node type  
**Lesson:** Each node type in n8n has its own versioning — changing a node's type requires updating the typeVersion accordingly

---

### Challenge 4 — Cost Optimization Architecture
**Problem:** Original workflow required 10+ paid service accounts costing $200-400/month  
**Solution:** Audited each service, identified functional equivalents, and reduced to 6 services saving an estimated 50-60% in monthly costs  
**Services eliminated:** Airtable (→ Google Sheets), Blotato (→ Buffer/manual), Serper.dev (→ YouTube Data API), ConvertAPI (→ native code node), Shotstack (partially consolidated)

---

## 📊 Project Stats

| Metric | Value |
|---|---|
| Total workflow nodes | 82 |
| APIs integrated | 6 |
| Services eliminated (cost optimization) | 4 |
| Estimated monthly cost savings | 50-60% |
| Credential types configured | 4 (Header Auth, Bearer, OAuth2, AWS v4) |
| Database tables created | 2 |
| Workflow phases | 4 |
| Trigger schedule | Every 8 hours |

---

## 🎯 Relevant Certifications & Career Paths

The skills in this project directly align with:

| Certification / Role | Relevant Skills |
|---|---|
| **CompTIA A+** | Troubleshooting methodology, system configuration |
| **AWS Cloud Practitioner** | S3 object storage, cloud services, API concepts |
| **Google IT Support Certificate** | Troubleshooting, networking, API authentication |
| **IT Support Specialist** | Credential management, service configuration |
| **Systems Administrator** | Service integration, automated scheduling, cloud storage |
| **DevOps Engineer** | CI/CD concepts, API pipelines, automation, infrastructure |
| **No-Code Developer** | n8n workflow design, API integration, database management |
| **Automation Engineer** | Multi-step workflow design, trigger-based automation |

---

## 🗺️ Roadmap

- [x] Set up n8n workflow automation environment
- [x] Configure OpenAI API integration
- [x] Configure ElevenLabs voiceover API
- [x] Set up Cloudflare R2 cloud storage
- [x] Design and build Airtable database schema
- [x] Configure Replicate video generation API
- [x] Configure Shotstack video assembly API
- [x] Import and configure 82-node workflow
- [x] Troubleshoot and resolve API authentication issues
- [x] Optimize architecture from 10+ services to 6
- [ ] Add $5 Replicate credits to complete first test run
- [ ] Verify full pipeline end-to-end execution
- [ ] Set up self-hosted n8n on Hetzner VPS
- [ ] Integrate File 2 (Content Intelligence Engine) for automated idea generation
- [ ] Add social media auto-publishing via Buffer
- [ ] Integrate File 1 (AI Asset Sprint) for ebook generation

---

## 📁 Repository Contents

```
ai-content-automation-pipeline/
│
├── README.md                          # This file
├── Content_Reactor_v2_MANUAL.json     # Main workflow (import to n8n)
├── IT_Portfolio_Project.docx          # Full project writeup (Word format)
└── docs/
    └── setup-guide.md                 # Step by step setup instructions
```

---

## 🚀 How to Use This Workflow

1. **Prerequisites** — Create accounts with: n8n, OpenAI, ElevenLabs, Replicate, Shotstack, Cloudflare R2, Airtable
2. **Set up credentials** — Configure all API keys in n8n's credential manager
3. **Set up Airtable** — Create base with `Production Queue` table (fields: Idea, Category, Tone, Status, Full Script, Video URL)
4. **Configure placeholders** — Replace `YOUR_R2_BUCKET_NAME` and `YOUR_R2_PUBLIC_URL` in the JSON file
5. **Import workflow** — In n8n: New Workflow → Import from file → select JSON
6. **Add a topic** — In Airtable add a row with Status = `New`, fill in Idea, Category, Tone
7. **Run** — Click Execute Workflow on the trigger node

---

## 📚 What I Learned

This project was my introduction to real-world API integration and automation. Key takeaways:

> Building automated systems requires understanding not just how each tool works individually, but how data flows and transforms between them. The most valuable skill wasn't knowing the APIs — it was methodically diagnosing why they weren't talking to each other correctly.

- Every API has its own authentication method — there's no one-size-fits-all approach
- Error messages are clues, not dead ends — trace them back to the source
- Cost optimization is an architectural skill, not just a budget concern
- Documentation and version control matter from day one

---

## 🔗 Connect

Feel free to reach out with questions about this project or to collaborate.

---

*Built as part of a personal IT learning and portfolio development journey — March 2026*
