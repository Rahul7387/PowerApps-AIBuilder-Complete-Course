# Module 07 — Advanced AI Builder Scenarios


---

## 🎯 Learning Objectives
- Design enterprise document automation architecture
- Apply AI governance and Responsible AI
- Optimize performance and credit consumption
- Implement security controls
- Build multi-step workflows with exception handling

---

## 📖 Theory

### 1. Enterprise Document Automation — 5 Layers

```
Layer 1: Ingestion (email, SharePoint upload, scanner, API)
    ↓
Layer 2: Classification (what type is this document?)
    ↓
Layer 3: Extraction (extract structured data from it)
    ↓
Layer 4: Validation (business rules — does total = sum of lines?)
    ↓
Layer 5: Action (approve, update ERP, archive, notify, escalate)
```

### 2. Responsible AI Principles (Microsoft)
1. **Fairness** — treat all people fairly
2. **Reliability & Safety** — perform reliably and safely
3. **Privacy & Security** — respect privacy, be secure
4. **Inclusiveness** — empower everyone
5. **Transparency** — be understandable
6. **Accountability** — humans accountable for AI systems

**Practical implementation:**

| Principle | How to implement |
|---|---|
| Transparency | Show confidence scores; label AI-generated content |
| Accountability | Log all AI decisions in Dataverse; admin dashboard |
| Human oversight | High-stakes decisions require human approval |
| Privacy | Don't include PII in training data unless necessary |

### 3. Exception Handling Patterns

| Exception | Cause | Handling |
|---|---|---|
| Low confidence | Poor document quality | Manual review queue |
| Missing fields | Field absent in document | Business rule default |
| API timeout | AI service slow | Retry with backoff |
| Invalid format | Corrupt file | Return error to submitter |
| Quota exceeded | Credits exhausted | Alert admin, pause |
| Business rule failure | Invalid data | Exception queue |

### 4. Performance Optimization

**Flow performance:**
- Enable concurrency in Apply to each (max 50 parallel)
- Filter at trigger — don't process everything, only what qualifies
- Cache static lookups — don't query Dataverse every loop iteration
- Use Compose to shape data before AI calls

**Credit optimization:**
- Use prebuilt models where possible
- Don't re-process unchanged documents
- Batch processing > one-at-a-time

---

## ✅ Best Practices — Day 7

1. **Design for failure from the start** — every AI call can fail; plan for it
2. **Build audit trail** — every AI decision: input + output + confidence + timestamp + user
3. **Human-in-the-loop for high stakes** — never fully automate financial/HR/legal decisions
4. **Monitor model drift** — retrain when accuracy drops below threshold
5. **Separate extraction from action** — extract in one flow, act in another (easier debugging)
6. **Version everything** — models, flows, and apps via solution versioning

---

## 🔬 Hands-On Lab — Multi-Document Automation Pipeline

### Scenario
Procurement receives 3 document types daily: invoices, purchase orders, delivery receipts. Build a pipeline that: classifies type, routes to correct extractor, validates, creates Dataverse records, and handles exceptions.

### Architecture
```
Document arrives (SharePoint)
  ↓
Flow 1: Document Classifier
  ↓ Invoice    ↓ PO      ↓ Receipt
Flow 2a     Flow 2b    Flow 2c
Extractor   Extractor  Extractor
  ↓
Validation (confidence + business rules)
  ↓              ↓
Dataverse    Exception Queue
  ↓
Approval (invoices > $5,000)
```

### Step 1 — Document Classifier Model
Train category classification (text from first 500 chars):
- Categories: Invoice, PurchaseOrder, DeliveryReceipt, Unknown

### Step 2 — Flow 1: Classifier
```
Trigger: File created in SharePoint /DocumentDropZone
Step 1: Get file content + extract text (OCR)
Step 2: AI Builder classify → Invoice / PO / Receipt / Unknown
Step 3: Switch
  Invoice → Run child flow: Invoice Extractor
  PO → Run child flow: PO Extractor
  Receipt → Run child flow: Receipt Extractor
  Unknown → Exception queue + Teams alert
```

### Step 3 — Extraction Flows (one per doc type)
```
Input: file content
Step 1: AI Builder Document Processing (custom model)
Step 2: Condition: confidence > 0.85 AND required fields present?
  YES: Add Dataverse row, move file to /Processed
  NO: Add to ExceptionQueue, Teams notification, keep in /NeedsReview
```

### Step 4 — Exception Review Canvas App
```
Gallery: ExceptionQueue (Status = PendingReview)
Click record: file preview + extracted fields (editable)
Buttons: "Approve & Process" | "Reject" | "Reassign"
Approve: create Dataverse record + update status = Resolved
```

### Step 5 — Approval for High-Value Invoices
```
After Invoice creation:
  If TotalAmount > 5000:
    Start and wait for approval (Finance Manager)
    Approved → Status = Approved
    Rejected → Status = Rejected, email requester
```

---

## 📋 POC Summary

**Project:** Enterprise Document Automation Pipeline

| Document Type | AI Model | Extractor |
|---|---|---|
| Invoice | Document Processing (custom) | Child flow |
| Purchase Order | Document Processing (custom) | Child flow |
| Delivery Receipt | Document Processing (custom) | Child flow |
| Unknown | Category Classification | Exception queue |

*Next: [Module 08 — Power Pages →](../Module-08-Power-Pages/README.md)*
