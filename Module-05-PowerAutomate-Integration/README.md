# Module 05 — AI Builder + Power Automate Integration


---

## 🎯 Learning Objectives
- Master AI Builder actions in Power Automate
- Build intelligent email routing
- Create AI-based approval system
- Implement proper error handling

---

## 📖 Theory

### AI Builder Actions in Power Automate

**Prebuilt (no training):** Extract from invoices/receipts, analyze sentiment, detect language, OCR, extract key phrases

**Custom model actions:** Your trained classification, document processing, object detection, prediction models

### Flow Trigger Types

| Trigger | Use When |
|---|---|
| Automated | React to events (email, file, Dataverse row) |
| Scheduled | Batch processing (daily at 8am) |
| Instant | On-demand (button click) |
| Business process | Data-driven (Dataverse change) |

### AI Flow Design Patterns

1. **Classify and Route:** Email → AI intent → route to correct team
2. **Extract and Store:** File → AI extract → Dataverse → notify
3. **Analyze and Alert:** Feedback → AI sentiment → if negative → alert manager
4. **Approve with AI Pre-screening:** Expense → AI validate → approval request

### Error Handling (Always Required)

On every AI Builder step:
1. Click **...** → **Configure run after**
2. Add branches for **has failed** and **has timed out**
3. In error branches: log, notify admin, update record status

---

## ✅ Best Practices — Day 5

1. **Always have a fallback path** — AI calls can fail; plan backup routes
2. **Use concurrency control** — limit parallel runs to prevent throttling
3. **Log every AI result** — store raw output in Dataverse for debugging
4. **Set timeout values** — AI calls taking > 2 min likely failed
5. **Use child flows** for reusable AI logic — don't copy the same block into 10 flows
6. **Monitor run history weekly** — watch for failure patterns
7. **Add delays between rapid AI calls** — avoid rate limits

---

## 🔬 Hands-On Lab — Support Ticket Classification Flow

### Scenario
Support emails arrive at support@company.com. AI reads each email, classifies it (IT/HR/Finance/Facilities), creates a Dataverse ticket, and posts to the correct Teams channel.

### Step 1 — Prerequisites
- Category classification model from Day 4 (or train new one for support tickets)
- Shared mailbox: support@company.com
- Dataverse table: SupportTickets
- Teams channels: IT-Tickets, HR-Tickets, Finance-Tickets, Facilities-Tickets

### Step 2 — Create Flow

```
TRIGGER: New email in support@company.com

Step 1: Compose — combine subject + body for AI

Step 2: AI Builder — Classify text (SupportTicketClassifier)
  Text: combined email content

Step 3: AI Builder — Analyze sentiment (prebuilt)
  Text: email body

Step 4: Dataverse — Add row (SupportTickets)
  Subject, Body, FromEmail, Category, Confidence, Sentiment, Status=New

Step 5: Switch on Category
  IT → post to IT-Tickets channel
  HR → post to HR-Tickets channel
  Finance → post to Finance-Tickets channel
  Facilities → post to Facilities-Tickets channel
  Default → email manager@company.com for manual review

Step 6 (run after FAILED): email automation-alerts@company.com
```

### Step 3 — Extend with Approval (Finance > $10,000)
After Finance case:
```
Condition: extracted amount > 10000
YES: Start approval → CFO
  Approved → Status = Approved
  Rejected → Status = Rejected, email requester
NO: Normal processing
```

### Step 4 — Test
Send test emails:
- "My laptop won't start" → IT
- "Need to update payroll bank account" → HR
- "Q3 expense report has wrong totals" → Finance
- "Air conditioning in Room 204 broken" → Facilities

---

## 📋 POC Summary

**Project:** Intelligent Support Ticket Router
**ROI:** Eliminates 2+ hours/day of manual sorting for 3 agents = 6 person-hours/day saved

*Next: [Module 06 — Power Apps Integration →](../Module-06-PowerApps-Integration/README.md)*
