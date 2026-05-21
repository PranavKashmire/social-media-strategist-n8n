# 📣 Social Media Strategist – AI Powered n8n Automation

> An end-to-end n8n workflow that automates the full job of a Social Media Strategist — researching trends, generating platform-specific AI content, publishing to LinkedIn, tracking campaigns, and reporting — completely hands-free every weekday morning.

---

## 🎯 What Problem Does This Solve?

A social media manager spends 2-3 hours every day on repetitive tasks:

- Researching what's trending in their industry
- Writing separate posts for LinkedIn and Twitter
- Publishing at the right time
- Logging what was posted
- Reporting on campaign performance

**This workflow automates all of it.** You add topics to a Google Sheet once — the workflow does everything else automatically at 9AM every weekday.

---

## ✨ What It Does

```
Every weekday at 9AM:

📋 Reads content calendar (Google Sheets)
      ↓
🔎 Searches Google for latest trends on that topic
      ↓
🤖 Gemini AI writes a LinkedIn post + Twitter post
      ↓
📤 Publishes directly to LinkedIn
      ↓
✅ Marks topic as posted in the sheet
      ↓
📊 Logs full campaign results
      ↓
🔔 Sends campaign report to Telegram
```

---

## 🗂️ Workflow Architecture

| # | Node | Layer | Purpose |
|---|------|-------|---------|
| 1 | ⏰ Daily Trigger 9AM | Scheduling & Automation | Fires automatically Mon–Fri at 9AM |
| 2 | 🗂️ Fetch Pending Topic | Content Calendar Management | Reads next pending topic from Google Sheets |
| 3 | 🔍 Topic Found? | Decision & Control Flow | Routes workflow — continue or send alert |
| 4 | 🔎 Search Trends (WebSearch) | Trend Research | Searches Google for live trends via SerpAPI |
| 5 | ⚙️ Extract Trend Data | Data Processing | Extracts top 3 trend headlines and snippets |
| 6 | 🤖 Gemini – Generate Posts | AI Content Generation | Writes LinkedIn + Twitter posts using Gemini 2.5 Flash |
| 7 | ⚙️ Parse Gemini Response | Data Transformation | Parses and structures AI output |
| 8 | 📤 Post to LinkedIn | Content Publishing | Publishes post to LinkedIn via OAuth2 API |
| 9 | ⚙️ Prepare Status Update | Campaign Tracking | Prepares row update data |
| 10 | ✅ Mark Topic as Posted | Content Calendar Update | Updates status pending → posted in sheet |
| 11 | 📊 Log Results to Sheet | Analytics & Performance Tracking | Appends full campaign log to sheet |
| 12 | 🔔 Campaign Report | Reporting & Notification | Sends full summary to Telegram |
| 13 | ⚠️ Notify – No Topics | Error Handling & Alert | Alerts when content calendar is empty |

---

## 🔗 How It Maps to the Social Media Strategist Agent

This workflow implements the [Social Media Strategist agent definition](./marketing-social-media-strategist.md) — converting every agent capability into an automated n8n node:

| Agent Capability (.md file) | Implemented By |
|-----------------------------|---------------|
| `WebSearch` tool | 🔎 Search Trends node — searches Google via SerpAPI |
| `WebFetch + Read` tools | ⚙️ Extract Trend Data node — processes search results |
| `Write` tool | 🤖 Gemini node — generates platform-specific content |
| `Edit` tool | ⚙️ Parse Gemini Response node — structures AI output |
| Content Calendar Management | 🗂️ Fetch Pending Topic + ✅ Mark as Posted |
| LinkedIn Mastery | 📤 Post to LinkedIn node |
| Scheduled Publishing | ⏰ Daily Trigger node |
| Analytics & Reporting | 📊 Log Results + 🔔 Campaign Report |
| Campaign Management | Full workflow orchestration |
| Error Handling | ⚠️ Notify – No Topics node |

---

## 🛠️ Tech Stack

| Tool | Purpose | Cost |
|------|---------|------|
| [n8n](https://n8n.io) | Workflow automation engine | Free (self-hosted) |
| [Google Gemini 2.5 Flash](https://aistudio.google.com) | AI content generation | Free tier (20 req/min) |
| [SerpAPI](https://serpapi.com) | Google trend search | Free tier (100 searches/month) |
| [Google Sheets](https://sheets.google.com) | Content calendar + logging | Free |
| [LinkedIn API](https://developer.linkedin.com) | Post publishing | Free (OAuth2) |
| [Telegram Bot API](https://core.telegram.org/bots) | Campaign notifications | Free |

---

## 📋 Google Sheet Structure

Create a Google Sheet with a tab named **Topics** and these exact column headers:

| Column | Description | Example |
|--------|-------------|---------|
| `topic` | Post topic | Why most social media strategies fail |
| `brand_voice` | Tone for the AI | Conversational, bold, data-backed |
| `platform_focus` | Target platform | both / linkedin / twitter |
| `status` | Post status | pending → posted (auto-updated) |
| `linkedin_post` | Auto-filled by workflow | (leave blank) |
| `twitter_post` | Auto-filled by workflow | (leave blank) |
| `content_theme` | Auto-filled by workflow | (leave blank) |
| `engagement_hook` | Auto-filled by workflow | (leave blank) |
| `posted_at` | Auto-filled by workflow | (leave blank) |

Set `status = pending` for any topic you want the workflow to pick up.

---

## 🚀 Setup Guide

### Prerequisites
- n8n installed (`npm install -g n8n` then `n8n start`)
- Google account
- LinkedIn account + Developer App
- Telegram account

---

### Step 1 — Get Your API Keys

**Google Gemini (Free)**
1. Go to [aistudio.google.com](https://aistudio.google.com)
2. Click **"Get API Key"** → **"Create API key"**
3. Copy your key

**SerpAPI (Free)**
1. Go to [serpapi.com](https://serpapi.com)
2. Sign up → copy your API key from dashboard

**LinkedIn Developer App**
1. Go to [developer.linkedin.com](https://developer.linkedin.com)
2. Click **"Create App"** → fill details
3. Go to **Auth** tab → copy Client ID and Client Secret
4. Add redirect URL: `http://localhost:5678/rest/oauth2-credential/callback`
5. Go to **Products** tab → request access to **"Share on LinkedIn"**

**Telegram Bot**
1. Open Telegram → search **@BotFather** → type `/newbot`
2. Follow steps → copy the bot token
3. Search **@userinfobot** → copy your Chat ID

---

### Step 2 — Set Up Google Sheet
1. Go to [sheets.google.com](https://sheets.google.com) → create new sheet
2. Name the tab `Topics`
3. Add column headers from the table above
4. Add your topics with `status = pending`
5. Copy your Sheet ID from the URL

---

### Step 3 — Import Workflow into n8n
1. Open n8n at `http://localhost:5678`
2. Click **"Add Workflow"** → **⋮ menu** → **"Import from file"**
3. Select `social_media_strategist_workflow.json`
4. All 13 nodes will appear on the canvas

---

### Step 4 — Configure Credentials

**Google Sheets:**
- Click **🗂️ Fetch Pending Topic** node
- Credential → Create new → Sign in with Google
- Paste your Sheet ID in the Document field
- Do the same for **✅ Mark Topic as Posted** and **📊 Log Results to Sheet**

**Gemini AI:**
- Click **🤖 Gemini – Generate Posts** node
- Replace `YOUR_GEMINI_API_KEY` in the URL with your key

**SerpAPI:**
- Click **🔎 Search Trends** node
- Replace `YOUR_SERPAPI_KEY` in the URL with your key

**LinkedIn:**
- Click **📤 Post to LinkedIn** node
- Credential → Create new → LinkedIn OAuth2
- Enter Client ID and Client Secret → Connect with LinkedIn
- Replace `YOUR_LINKEDIN_MEMBER_ID` with your member ID

**Telegram:**
- Click **🔔 Campaign Report** node
- Credential → Create new → paste Bot Token
- Replace `YOUR_TELEGRAM_CHAT_ID` with your Chat ID
- Do the same for **⚠️ Notify – No Topics**

---

### Step 5 — Run It!
1. Press **Ctrl + S** to save
2. Click **"Execute Workflow"** ▶️ to test
3. Check your Google Sheet — first topic should update to `posted`
4. Check LinkedIn — post should be live
5. Check Telegram — campaign report should arrive

**To run automatically:**
Click **"Publish"** → toggle workflow **Active** → it will run every weekday at 9AM

---

## 📊 What the Telegram Report Looks Like

```
✅ Campaign Published!

📌 Topic: Why most social media strategies fail in the first 30 days

🔎 Trends Used:
1. LinkedIn engagement drops 20% in first month...
2. Social media ROI measurement challenges...

💼 LinkedIn Preview:
Here's a brutal truth most brands don't want to hear...

🐦 Twitter:
Most social strategies fail in 30 days. Not because of bad content. Because of bad expectations. #SocialMedia #Marketing

🎯 Engagement Hook:
Post ends with a direct question about personal experience

📅 Best Post Time: 9AM Tuesday

💡 Follow-up Idea:
"The 5 metrics that actually predict social media success"

🕐 Generated at 2026-05-21T09:00:00.000Z
```

---

## 🔄 How Topics Rotate Automatically

```
Day 1: Row 2 (status: pending → posted) ✅
Day 2: Row 3 (status: pending → posted) ✅
Day 3: Row 4 (status: pending → posted) ✅
...
Day 10: Row 11 (status: pending → posted) ✅
Day 11: No topics → Telegram alert sent ⚠️
```

When all topics are posted, you get a Telegram alert to add more topics.

---

## ❌ What's Not Covered (and Why)

| Capability | Reason Not Implemented |
|-----------|----------------------|
| Twitter/X posting | API requires $200/month paid plan |
| Reply to comments | LinkedIn API premium access required |
| LinkedIn Analytics | Requires LinkedIn partner API access |
| LinkedIn Advertising | Requires Marketing API approval |
| A/B Testing | Needs historical performance data |
| Competitive Benchmarking | Requires paid social listening tools |
| Instagram posting | Separate agent — out of scope |
| Reddit community | Separate agent — out of scope |

---

## 🗂️ Project Structure

```
social-media-strategist-n8n/
│
├── social_media_strategist_workflow.json   ← Import this into n8n
├── marketing-social-media-strategist.md   ← Agent definition file
├── social_media_topics.xlsx               ← Pre-filled content calendar
└── README.md                              ← This file
```

---

## 💡 Future Improvements

- Add LinkedIn Analytics polling to track post performance
- Feed performance data back into Gemini prompt for smarter content
- Add Telegram command bot to trigger posts manually
- Add approval gate — review post on Telegram before it goes live
- Add Instagram posting via Meta API
- Add competitor analysis using additional SerpAPI queries

---

## 📄 License

MIT License — free to use, modify and distribute.

---

## 🙋 Author

Built as part of a Social Media Strategist AI Agent automation exercise using n8n, Google Gemini, SerpAPI, LinkedIn API, and Telegram.
