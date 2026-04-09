# n8n AI Automation — Demo Project

> Source code for the YouTube tutorial: **"n8n AI Automation for Beginners 2026"**

Master n8n AI automation in this complete 2026 tutorial! Learn to build powerful AI workflows, automate tasks, and streamline your workflow processes from scratch.
In this video, you'll learn:
• Setting up n8n AI automation
• Building custom workflows
• Integrating AI services
• Automating repetitive tasks
• Best practices for 2026

<div align="center">
  <a href="https://youtu.be/jCWPqTDgplk?si=kTNnuNYyllslMwEq">
    <img src="https://img.youtube.com/vi/kTNnuNYyllslMwEq/maxresdefault.jpg" alt="Watch the video" style="width:100%;">
  </a>
</div>

---

## What This Builds

Two fully working n8n AI automation workflows:

1. **AI Q&A Bot** — Webhook receives a question → Claude answers it → logs to Google Sheets → notifies Slack → returns JSON response
2. **AI Daily Newsletter** — Runs every weekday at 8am → fetches top 5 tech headlines from RSS → Claude writes a briefing → emails it to you

---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- An [Anthropic API key](https://console.anthropic.com) (claude-haiku-4-5-20251001 is cheap — ~$0.00025 per 1K tokens)
- Port `5678` free on your machine

---

## Step 1 — Start n8n

```bash
# Option A: Docker Compose (recommended — data persists)
docker-compose up -d

# Option B: Single command (quick test)
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  -e N8N_BASIC_AUTH_ACTIVE=true \
  -e N8N_BASIC_AUTH_USER=admin \
  -e N8N_BASIC_AUTH_PASSWORD=admin123 \
  -e WEBHOOK_URL=http://localhost:5678/ \
  docker.n8n.io/n8nio/n8n
```

Open: **http://localhost:5678**
Login: `admin` / `admin123`

---

## Step 2 — Import the Workflows

1. In n8n UI: click **Workflows** in the left sidebar
2. Click **Import from file**
3. Select `workflow-ai-qabot.json` → Import
4. Repeat for `workflow-ai-newsletter.json`

---

## Step 3 — Add Your Credentials

### Anthropic Claude (required for both workflows)
1. Get your API key from [console.anthropic.com](https://console.anthropic.com) → API Keys → Create Key
2. In n8n: **Settings → Credentials → Add Credential**
3. Search **"Header Auth"** → select it
4. Name: `Anthropic API Key` · Value: your key (starts with `sk-ant-...`) → Save

### Google Sheets (optional — for Q&A bot logging)
1. Add Credential → search "Google Sheets OAuth2"
2. Follow the OAuth flow (Google Cloud Console → create OAuth client)
3. Paste Client ID + Secret → Connect

### Slack (optional — for Q&A bot notifications)
1. Go to [api.slack.com](https://api.slack.com/apps) → Create App → OAuth
2. Add `chat:write` scope → Install to workspace
3. Copy Bot Token → Add Credential in n8n → paste token

### Gmail (required for newsletter)
1. Add Credential → search "Gmail OAuth2"
2. Follow the OAuth flow
3. Connect your Gmail account

---

## Step 4 — Test the AI Q&A Bot

1. Open `workflow-ai-qabot` → click **Test workflow** once to register the webhook
2. Activate the workflow with the toggle (top right)
3. Copy the **Production Webhook URL** from the Webhook node

```bash
curl -X POST http://localhost:5678/webhook-test/ai-qa-bot \
  -H "Content-Type: application/json" \
  -d '{"question": "What is n8n in one sentence?"}'
```

Expected response:
```json
{
  "question": "What is n8n in one sentence?",
  "answer": "n8n is an open-source workflow automation platform that lets you connect 400+ apps and APIs using a visual node-based editor — with the power to write custom code when needed.",
  "model": "claude-haiku-4-5-20251001",
  "timestamp": "2026-03-07T08:00:00.000Z"
}
```

---

## Step 5 — Test the Newsletter

1. Open `workflow-ai-newsletter`
2. Set your email address in the Gmail node
3. Click **Test workflow** — it runs immediately (ignores the schedule)
4. Check your inbox in ~30 seconds

To activate the daily schedule: toggle ON in the top right.

---

## File Structure

```
demo/
├── docker-compose.yml           # n8n container with persistent storage
├── workflow-ai-qabot.json       # Import: AI Q&A Bot workflow
├── workflow-ai-newsletter.json  # Import: AI Daily Newsletter workflow
└── README.md                    # This file
```

---

## Useful Docker Commands

```bash
# Check n8n status
docker ps

# View n8n logs
docker logs -f n8n

# Stop n8n
docker-compose down

# Stop and wipe all data (full reset)
docker-compose down -v

# Restart n8n
docker-compose restart n8n
```

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Port 5678 already in use | Change `5678:5678` to `5679:5678` in docker-compose.yml |
| Webhook URL not working | Make sure `WEBHOOK_URL` matches your server address |
| Anthropic 401 error | Check your API key in Header Auth credential; key starts with `sk-ant-` |
| Google Sheets permission error | Re-run OAuth flow; ensure Sheets API is enabled in Google Cloud |
| Workflow not triggering on schedule | Check timezone setting in docker-compose.yml |

---

## Resources

- [n8n Documentation](https://docs.n8n.io)
- [n8n Community Workflows](https://n8n.io/workflows/)
- [Anthropic API Console](https://console.anthropic.com)
- [Anthropic API Pricing](https://www.anthropic.com/pricing)
- [n8n Docker Hub](https://hub.docker.com/r/n8nio/n8n)
