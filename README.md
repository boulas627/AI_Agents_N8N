# 🤖 N8N AI Agents

A collection of production-ready AI agents built in [N8N](https://n8n.io/), designed for busy professionals looking to automate intelligent, context-aware workflows. Each agent is a true agentic system — not just a pipeline — with LLM reasoning, tool access, and memory.

---

## 📋 Table of Contents

- [What Makes These True AI Agents](#what-makes-these-true-ai-agents)
- [Agents](#agents)
  - [Email Priority Assistant](#-email-priority-assistant)
- [Getting Started](#getting-started)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Contributing](#contributing)

---

## What Makes These True AI Agents

Unlike simple automations that pass data through a fixed pipeline, each workflow here is built on the **ReAct (Reason + Act)** pattern:

| Capability | Description |
|---|---|
| 🧠 **LLM Reasoning** | The model decides *what* to do, not just *how* to format output |
| 🛠️ **Tool Use** | The agent calls external tools (Gmail, Calendar, etc.) on demand |
| 💾 **Memory** | Context is retained across steps within a session |
| 🔁 **Feedback Loop** | The agent observes tool results and adjusts its next action |

---

## Agents

### 📧 Email Priority Assistant

**File:** [`Email_Assistant_v2.json`](./email-priority-assistant/Email_Assistant_v2.json)

Runs daily at 3:00 PM, autonomously checks your Gmail inbox, classifies emails by urgency, cross-references your Google Calendar for deadline context, and delivers a formatted HTML priority digest — only when there's something that actually needs your attention.

#### How It Works

```
Schedule Trigger (3PM daily)
        ↓
Email Priority Analyzer ── [GPT-4o / Claude]
        ├── 📧 Gmail Tool          → fetches unread inbox emails (up to 25)
        ├── 📅 Google Calendar Tool → reads upcoming 7 days of events
        └── 💾 Window Buffer Memory → retains session context
        ↓
Format Email Summary (Code Node)
        ↓
Has Priority Emails? (IF Node)
        ├── YES → Send HTML Digest to your inbox
        └── NO  → Stop (no noise on quiet days)
```

#### Priority Criteria

| Priority | Criteria |
|---|---|
| 🔴 **HIGH** | Contains `BSSL` · Application security meetups · Cybersecurity job opportunities · Urgent deadlines within 24h |
| 🟡 **MEDIUM** | LinkedIn messages and notifications · Soft follow-ups · FYI updates needing eventual response |
| ⚪ **Ignored** | Newsletters · Automated alerts · No-reply senders · Low-importance mail |

#### What the Agent Decides (Not You)

- **Which emails to fetch** — the agent calls the Gmail tool with its own judgment, not a pre-set filter
- **Whether calendar context matters** — if an email mentions a meeting or deadline, the agent checks your calendar before assigning priority
- **What action to recommend** — each flagged email includes a suggested next step (reply, review, schedule, or ignore)

#### Sample Output Email

```
📧 Priority Email Summary — May 05, 2025
2 High Priority · 1 Medium Priority

🔴 HIGH PRIORITY (2)
─────────────────────────────────────────
Subject:  BSSL May Meetup — Speaker Confirmation Needed
From:     meetup@bssl.org
Summary:  Requesting confirmation of your speaking slot by Friday.
Flagged:  Contains 'BSSL', action required within 24h
Action:   Reply to confirm or reschedule

Subject:  Senior AppSec Engineer — Fintech Startup (YC-backed)
From:     recruiter@talent.io
Summary:  Inbound recruiter outreach for a senior security role.
Flagged:  Cybersecurity job opportunity
Action:   Review and reply if interested

🟡 MEDIUM PRIORITY (1)
─────────────────────────────────────────
Subject:  John Doe accepted your LinkedIn connection
From:     messages-noreply@linkedin.com
Summary:  LinkedIn connection acceptance notification.
Flagged:  LinkedIn notification
Action:   Review profile when convenient
```

#### Setup Instructions

1. **Import the workflow** — In N8N, go to the top-right menu → *Import from file* → select `Email_Assistant_v2.json`

2. **Connect credentials** — The workflow requires three OAuth connections:

   | Credential | Node | How to connect |
   |---|---|---|
   | Gmail OAuth2 | Get Inbox Emails · Send Email Summary | N8N Credentials → New → Gmail OAuth2 |
   | Google Calendar OAuth2 | Get Calendar Events | N8N Credentials → New → Google Calendar OAuth2 |
   | OpenAI API Key | OpenAI Chat Model | N8N Credentials → New → OpenAI API |

3. **Configure the agent** — In the `Email Priority Analyzer` node, update the system prompt priority criteria to match your own inbox patterns (replace `BSSL` with your own keywords)

4. **Set your email address** — In the `Send Email Summary` node, update `sendTo` to your email address

5. **Activate** — Toggle the workflow to *Active*. It will run automatically at 3:00 PM daily.

#### Customization

- **Change trigger time** — Edit the `Schedule Trigger` node → `triggerAtHour`
- **Add priority keywords** — Edit the system prompt in `Email Priority Analyzer` to add your own HIGH/MEDIUM criteria
- **Swap the LLM** — Replace the `OpenAI Chat Model` node with an `Anthropic Chat Model` node using `claude-sonnet-4-6` if you hit OpenAI rate limits
- **Adjust inbox size** — In `Get Inbox Emails`, change `maxResults` (default: 25) up or down based on your volume

#### Requirements

- N8N v1.0+ (self-hosted or cloud)
- OpenAI API key with GPT-4o access **or** Anthropic API key
- Gmail account with OAuth2 access enabled
- Google Calendar OAuth2 access enabled

---

## Getting Started

### Prerequisites

- N8N instance (self-hosted via Docker or [n8n.io cloud](https://n8n.io/cloud/))
- API keys for whichever LLM you choose (OpenAI or Anthropic)
- OAuth2 credentials for Google services

### Importing Any Agent

1. Clone this repository
   ```bash
   git clone https://github.com/your-username/n8n-ai-agents.git
   ```

2. Open your N8N instance

3. Click the **≡ menu** (top right) → **Import from file**

4. Select the `.json` file for the agent you want

5. Connect your credentials in each node that shows a credential warning

6. Activate the workflow

---

## Tech Stack

| Component | Technology |
|---|---|
| Automation platform | [N8N](https://n8n.io/) |
| Primary LLM | OpenAI GPT-4o / GPT-4o-mini |
| Alternative LLM | Anthropic Claude Sonnet |
| Email integration | Gmail OAuth2 |
| Calendar integration | Google Calendar OAuth2 |
| Agent pattern | ReAct (Reason + Act) via N8N LangChain Agent node |

---

## Repository Structure

```
n8n-ai-agents/
├── README.md
├── email-priority-assistant/
│   ├── Email_Assistant_v2.json     # Importable N8N workflow
│   └── README.md                   # Agent-specific docs (this file)
└── ... (future agents added here)
```

---

## Contributing

This is a personal collection growing over time. If you have suggestions for new agents or improvements to existing ones, feel free to open an issue or PR.

---

*Built with N8N · Powered by LLMs · Designed for busy professionals*
