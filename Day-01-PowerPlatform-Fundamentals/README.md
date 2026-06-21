# Day 01 — Power Platform & AI Builder Fundamentals

**Duration:** 2 Hours | **Faculty:** Krisnendu Roy

---

## 🎯 Learning Objectives

- Understand the Microsoft Power Platform ecosystem
- Know what AI Builder is and when to use it
- Understand licensing and environment strategy
- Create your first AI-powered automation

---

## 📖 Theory

### 1. Microsoft Power Platform — The 5 Pillars

| Product | Purpose | When to use |
|---|---|---|
| **Power Apps** | Build custom apps (canvas, model-driven) | Replace spreadsheets and paper forms |
| **Power Automate** | Automate workflows and processes | Repetitive tasks, approvals, notifications |
| **Power BI** | Business intelligence and reporting | Dashboards, analytics, executive reporting |
| **Power Pages** | External-facing websites | Customer portals, partner portals |
| **Copilot Studio** | AI chatbots and agents | Self-service, FAQ, conversations |

**The glue — Dataverse:**
Dataverse is the enterprise data platform connecting all Power Platform products. Think of it as the brain of the platform — secure, scalable, relational, with built-in business logic.

```
Power Apps ──┐
Power Automate ──┤
Power Pages ──┤──→  Dataverse  ←──→  Azure / M365 / Dynamics 365
Copilot Studio ──┤
AI Builder ──┘
```

### 2. AI Builder — What Is It?

AI Builder is Microsoft's no-code/low-code AI capability embedded in Power Platform. It lets makers build, train, and use AI models without data science expertise.

> **Trainer's 20-year insight:** Most organizations don't need a data scientist to automate 80% of their AI use cases. AI Builder handles document processing, image recognition, prediction, and text analysis — the everyday AI that drives real business value.

### 3. AI Builder Model Types

| Model Type | What It Does | Example |
|---|---|---|
| **Document processing** | Extract fields from structured docs | Invoice → vendor, amount, date |
| **Object detection** | Identify items in images | Inventory count from photo |
| **Prediction** | Predict binary/multi-class outcomes | Will this customer churn? |
| **Category classification** | Classify text into categories | Ticket → IT / HR / Finance |
| **Sentiment analysis** | Detect sentiment in text | Positive / Negative / Neutral |
| **Text recognition (OCR)** | Extract text from images | Business card scan |
| **Prompt (GPT)** | AI-generated responses and summaries | Summarize email, draft reply |

### 4. Licensing & Environments

**AI Builder Credits:**
| Action | Cost |
|---|---|
| Document processing (per page) | 1 credit |
| Object detection (per image) | 1 credit |
| Prediction (per row) | 1 credit |
| Text classification (per item) | 1 credit |

**Environment strategy (non-negotiable best practice):**
```
DEV → TEST → PROD
Never build directly in production.
```

### 5. Architecture — How It Connects

```
User → Power Apps → Power Automate → AI Builder → Dataverse
```

---

## ✅ Best Practices — Day 1

1. **Always use a DEV environment** — never experiment in production
2. **Name environments clearly** — CONTOSO-ITAPP-DEV, CONTOSO-ITAPP-PROD
3. **Use Dataverse from Day 1** — SharePoint lists hit limits fast
4. **Monitor AI Builder credit usage** — set up alerts before hitting limits
5. **Start with prebuilt models** — check if a prebuilt model does the job before building custom
6. **Document your AI use case first** — what goes in, what comes out, who uses it

---

## 🔬 Hands-On Lab — First AI Flow

### Scenario
You receive vendor invoices via email. Automatically extract the invoice amount and vendor name and store them in SharePoint.

### Step 1 — Explore AI Builder Studio
1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Left nav → **AI Builder** → **Models**
3. Click **Explore** on "Invoice processing" — note it requires no training

### Step 2 — Create Power Automate Flow
1. [make.powerautomate.com](https://make.powerautomate.com) → **Create** → **Automated cloud flow**
2. Name: `Invoice Processing - Day 1 Lab`
3. Trigger: **When a new email arrives (V3)**
4. Click **Create**

### Step 3 — Configure Email Trigger
- Folder: Inbox
- Include Attachments: Yes
- Only with Attachments: Yes
- Subject Filter: Invoice

### Step 4 — Add AI Builder Action
1. **+ New step** → search `AI Builder`
2. Select: **Extract information from invoices**
3. Invoice file content: Dynamic content → **Attachments Content**
4. Invoice file name: Dynamic content → **Attachments Name**

### Step 5 — Store Results in SharePoint
1. **+ New step** → **SharePoint** → **Create item**
2. List Name: Invoices
3. Map: VendorName → `Vendor name`, Amount → `Invoice total`, Date → `Invoice date`

### Step 6 — Add Error Handling (Best Practice)
1. On the AI Builder step → **... → Configure run after**
2. Add parallel branch for **has failed**: send email alert to admin
3. This prevents silent failures in production

### Step 7 — Test
1. Save the flow
2. Send yourself a test email with "Invoice" in subject + PDF attachment
3. Check your SharePoint list after 2 minutes

### Expected Outcome
Invoices received by email automatically populate SharePoint — zero manual data entry.

---

## 📋 Lab Summary

| Built | Skills Gained |
|---|---|
| Email-to-SharePoint invoice flow | Email trigger, prebuilt AI model, SharePoint action |
| Error notification branch | Parallel branches, run-after configuration |

---

*Next: [Day 02 — Form Processing →](../Day-02-Form-Processing/README.md)*
