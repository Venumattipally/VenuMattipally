# AI Automation & Backend Engineering Portfolio — Venu Mattipally

Building production-ready systems using:
- AI Automation (n8n, LLMs)
- Backend Development (Java, Spring Boot)
- API Design & System Integration

Focused on solving operational bottlenecks in:
- Customer Support
- Lead Management
- CRM Automation
-Client Enquiry Agent
-Websacraping using Firecrawl
---

## 🔥 Featured Projects

### 1. AI-Powered Gmail Customer Support Agent
Automates email triage, classification, and response for EdTech companies handling 100–1000+ emails/day.

**Key Capabilities:**
- Intent classification (refund, billing, login issues, etc.)
- Sentiment & urgency detection
- FAQ-based auto-response (zero hallucination)
- Smart escalation to human agents
- Repeat customer detection

**Tech Stack:**
n8n • Google Gemini • Gmail API • Google Sheets

👉 Demo Loom Video 1 : https://www.loom.com/share/3adc9685668049e390d057ce1d93ae49
👉 Demo Loom Video 1  : https://www.loom.com/share/f1ad456c6f8c4ae2b6e6c88a18a7a68e
---

### 2. Lead Management System (EdTech)
Processes incoming leads, enriches data, scores them based on ICP fit and engagement, and syncs to CRM.

**Key Capabilities:**
- Schema validation for incoming leads
- Data enrichment (company, role, etc.)
- Lead scoring engine (0–100)
- Tiering: Hot / Warm / Cold
- CRM sync (HubSpot)
- Slack alerts for high-priority leads

**Tech Stack:**
n8n • JavaScript • HubSpot • Google Sheets • Slack

👉 Demo Loom Video : https://www.loom.com/share/33226e653c90424fbdf11e17c884802a
---

# 🤖 AI Client Intake Agent — n8n + Claude + Notion

> End-to-end automation that receives raw inbound enquiries, extracts structured data, makes a triage decision, and drafts a personalised reply — with zero manual steps.

---

## 📌 What This Solves

Every consultancy, agency, or freelancer manually reads inbound enquiries, figures out what the client needs, decides whether to pursue it, and writes a reply. For high-volume pipelines, this is a bottleneck — unqualified leads get the same time as real opportunities.

This automation handles the entire intake loop:

```
Tally Form → n8n Webhook → Claude API → Structured JSON → Notion Database + Draft Reply
```

No manual reading. No copy-paste. No generic template replies.

---

## 🎬 Demo

**[▶ Watch the Loom walkthrough](#)** — https://www.loom.com/share/e13f5fe14c914e6dae235fc804d5b78e
agent processing 5 real enquiries, edge case handling, architecture explained.

---

## ✨ Key Features

### 🧠 Intelligent Extraction
Even from vague, typo-filled, or near-blank inputs, the agent extracts:
- Company name, customer email, customer name, business type
- Pain point and desired outcome (inferred when not stated)
- Scope signals — tools, team sizes, volumes explicitly mentioned
- Missing info — gaps that would block proper scoping

### 📊 Triage Engine
Every enquiry gets a decision:
- **Feasibility** — High / Medium / Low / Not a fit
- **Complexity score** — 1 to 10 scale with explicit reasoning
- **Next step** — Discovery call / Request more info / Decline
- **One-line reasoning** — specific to each enquiry, never generic

### ⚙️ Implementation Planning (when feasible)
- Proposed tool stack tailored to the client's context
- Ordered key steps for implementation
- Anticipated risks and blockers
- Estimated timeframe

### ❓ Clarifying Questions (when info is insufficient)
- 2–5 targeted questions — not generic, each tied to a specific gap
- Each question includes a "why this matters" explanation
- Surfaces only when the AI determines more info is genuinely needed

### ✉️ Draft Reply
- References the client's actual company, tools, and pain point
- Correct tone per outcome: discovery call CTA / questions woven in naturally / warm decline
- 120–200 words — no filler phrases, no templates

### 🔁 Email Deduplication
- Checks submitted email against the Notion database before processing
- If duplicate detected: blocks the workflow, logs the original submission details, surfaces prior triage decision
- Override option available for intentional re-processing

---

## 🗂️ Output Structure (Notion Database)

Each enquiry creates a structured Notion record with 17 properties:

| Property | Type | Description |
|---|---|---|
| Company | Title | Primary identifier |
| Customer Name | Text | Contact person |
| Customer Email | Email | Used for deduplication |
| Business Type | Select | Inferred from signals |
| Pain Point | Text | Core problem |
| Desired Outcome | Text | What success looks like |
| Scope Signals | Multi-select | Tools/systems mentioned |
| Missing Info | Text | Gaps blocking scoping |
| Feasibility | Select | High / Medium / Low / Not a fit |
| Complexity | Number | 1–10 scale |
| Next Step | Select | Discovery Call / Request More Info / Decline |
| Reasoning | Text | One-line triage rationale |
| Tool Stack | Text | Recommended tools |
| Estimated Timeframe | Text | Implementation estimate |
| Draft Reply | Text | Full personalised reply |
| Enquiry Date | Date | Submission timestamp |
| Status | Select | New / In Review / Replied |

---

## 🏗️ Architecture

```
┌─────────────────┐
│   Tally Form    │  ← Customer name, email, company, requirements
└────────┬────────┘
         │ POST webhook
         ▼
┌─────────────────┐
│  n8n Webhook    │  ← Receives raw form data
│    Trigger      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Notion Check   │  ← Query database for existing email
│  (Dedup Node)   │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
 Duplicate  New lead
    │         │
    ▼         ▼
 [Stop]  ┌─────────────────┐
         │  HTTP Request   │  ← Claude Sonnet API
         │  (Claude API)   │     Structured JSON prompt
         └────────┬────────┘
                  │
                  ▼
         ┌─────────────────┐
         │  Code Node      │  ← Parse + validate JSON response
         │  (JS Parser)    │
         └────────┬────────┘
                  │
         ┌────────┴────────┐
         ▼                 ▼
┌─────────────────┐  ┌─────────────────┐
│  Notion Create  │  │  Gmail / Email  │
│  Database Row   │  │  Draft Reply    │
└─────────────────┘  └─────────────────┘
```

---

## 🔧 Tech Stack

| Layer | Tool |
|---|---|
| Automation engine | n8n (self-hosted or cloud) |
| AI model | Claude Sonnet (`claude-sonnet-4-20250514`) |
| Intake form | Tally.so |
| Database | Notion |
| Email | Gmail via n8n |
| Deduplication | Notion query + n8n IF node |
| Auth | API key headers, Notion OAuth |

---

## 📋 System Prompt Design

The system prompt is engineered for real-world messy inputs — not demo-perfect enquiries. Key design decisions:

**Graceful degradation** — blank or vague inputs don't break the workflow. They produce a valid "early exploration stage" triage with appropriate clarifying questions.

**Inference-first** — the model attempts to derive business type from email domain and company name before marking a field as null.

**Edge case playbook** — four explicit cases handled: blank input, extremely vague input, incoherent text, and multiple unrelated requests.

**Draft reply guardrails** — banned openers list + "if this reply could go to any client, rewrite it" rule eliminates generic output.

**Strict JSON contract** — `applicable` flags on `implementation_idea` and `clarifying_questions` control which sections appear, preventing hallucinated plans on vague leads.

---

## 🚀 Setup Guide

### Prerequisites
- n8n instance (cloud or self-hosted)
- Geoq API key (Groq Console)
- Notion account + integration token
- Tally.so account (free)

### 1. Notion Database
Create a database with the 17 properties listed above. Connect your integration at [notion.so/my-integrations](https://www.notion.so/my-integrations).

### 2. Tally Form
Fields: Customer name / Customer email / Company name / Requirements. Point the webhook at your n8n URL.

### 3. n8n Workflow
Import the workflow JSON (see `/workflow/intake-agent.json`) and configure:
- Webhook node: set your path
- HTTP Request node: add your Anthropic API key header
- Notion nodes: add your integration token and database ID
- Gmail node: connect your Google account

### 4. Deduplication
The IF node checks the Notion query result. If a record with the same email exists, the workflow branches to a stop node and logs the duplicate.
---

## 💡 What I'd Improve with More Time

- **Human-in-the-loop approval** — Slack notification asking team to approve the reply before Gmail sends it
- **Confidence scoring** — flag extractions where the model had to infer heavily, for human review
- **Multi-language support** — detect language, reply in the same language
- **Follow-up trigger** — if Status stays "New" for 48 hours, auto-send a gentle follow-up
- **Webhook signature verification** — HMAC validation on the Tally webhook for security
- **Retry logic** — if Claude returns malformed JSON, re-prompt with the original input
---

*Built as part of a client intake automation assignment. Demonstrates end-to-end n8n workflow design, Groq API integration, Notion database automation, and production-ready error handling.*

## 🧠 My Approach

I focus on:
- Building **production-ready workflows**, not demos
- Designing systems around **real-world constraints**
- Ensuring **clear separation of logic (modular architecture)**
- Prioritizing **business impact over technical complexity**

---

## 📫 Let’s Connect

- 💼 [LinkedIn](https://www.linkedin.com/in/venumattipally/)
- 🧑‍💻 [GitHub](https://github.com/Venumattipally)
- 📧 Email:venumattipally@gmail.com

---
