# Module 10 — Power Platform Best Practices & ALM


---

## 🎯 Learning Objectives
- Implement enterprise environment strategy
- Master solution management and ALM
- Apply naming conventions consistently
- Configure security roles correctly
- Set up deployment pipelines

---

## 📖 Theory

### 1. Environment Strategy — Non-Negotiable

**The Golden Rule:** Every application needs minimum 3 environments.

```
DEV → TEST → PROD
(build)  (UAT)   (live)
```

**Environment naming:**
```
{OrgCode}-{AppCode}-{Env}
Examples: CONTOSO-ITAPP-DEV / CONTOSO-ITAPP-TEST / CONTOSO-ITAPP-PROD
```

**Never use the Default environment** for real applications.

### 2. Solution Management

| | Unmanaged | Managed |
|---|---|---|
| Can edit | ✅ Yes | ❌ No |
| Deleted on removal | ❌ No | ✅ Yes |
| Used in | DEV | TEST and PROD |

> One solution per application. Never mix multiple apps in one solution.

### 3. Naming Conventions — Apply from Day 1

**Why it matters:** Impossible to rename in production without breaking references. I've seen teams spend weeks renaming components in enterprise deployments.

**Table naming:** `{prefix}_TableName` → `cst_ITTicket`
**Column naming:** `{prefix}_columnName` → `cst_ticketNumber`

**Canvas App controls:**

| Prefix | Control | Example |
|---|---|---|
| txt | Text input | txtFirstName |
| lbl | Label | lblTitle |
| btn | Button | btnSubmit |
| gal | Gallery | galTickets |
| frm | Form | frmDetail |
| drp | Dropdown | drpCategory |
| var | Variable | varCurrentUser |
| col | Collection | colTickets |
| scr | Screen | scrHome |

**Flow naming:** `[Trigger] — [Action]` → `Email received — Create IT ticket`
**AI Builder models:** `[Domain][Type]` → `InvoiceDocumentProcessor`, `TicketCategoryClassifier`

### 4. Application Lifecycle Management (ALM)

```
Plan → Build → Test → Release → Monitor → Improve → Plan...
```

**ALM Maturity Levels:**

| Level | What | Tools |
|---|---|---|
| 1 | Manual export/import | Solution export/import |
| 2 | Automated source control export | Power Platform Build Tools |
| 3 | CI/CD pipeline | Azure DevOps / GitHub Actions |
| 4 | Full DevOps with testing | Automated tests, quality gates |

### 5. Security Roles — Least Privilege Always

```
System Administrator ← absolute minimum people
System Customizer ← makers only
App-specific custom roles ← your app users
Basic User ← minimum for app access
```

> **I see this mistake constantly:** Teams give everyone System Administrator "because it's easier." This is a critical security risk. Always create least-privilege custom roles.

### 6. Environment Variables

Use for every value that differs between environments:
- SharePoint URLs, API endpoints, email addresses, thresholds

**In Power Automate:** Get environment variable value action
**In Canvas Apps:** `'YourPublisher.VariableName'`

### 7. Top 10 Best Practices (20 Years of Experience)

1. **Solution-first always** — retrofitting is extremely painful
2. **Naming conventions from Day 1** — impossible to fix later without breaking things
3. **Environment variables for every URL/endpoint** — never hardcode
4. **Connection references for every connector** — #1 deployment headache when missing
5. **Test as a non-admin user** — most bugs only appear with least-privilege roles
6. **Export to source control weekly** — at minimum; ideally every meaningful change
7. **Document your data model** — saves hours in onboarding and maintenance
8. **Set up monitoring before go-live** — don't wait for users to report failures
9. **Plan your rollback** — always have previous solution version ready
10. **Schedule regular reviews** — monthly: unused components, orphaned flows, expired connections

---

## 🔬 Hands-On Lab — Deployment Pipeline Setup

### Scenario
Package the IT Helpdesk app from this course into a proper solution and deploy through DEV → TEST → PROD.

### Phase 1 — Create the Solution

#### Step 1 — Create Solution
1. [make.powerapps.com](https://make.powerapps.com) → **Solutions** → **+ New solution**
2. Name: `Contoso IT Helpdesk`
3. Create publisher: `Contoso`, prefix: `cst`
4. Version: `1.0.0.0`

#### Step 2 — Add Components
In the solution → **Add existing**:
- Canvas app: AI Helpdesk
- Cloud flows: all IT Helpdesk flows
- AI Builder models: IT Ticket Analyzer, Feedback Classifier
- Tables: SupportTickets, ITTicketComments
- Environment variables (create 4):

| Variable | Type | DEV Value | PROD Value |
|---|---|---|---|
| cst_SharePointSiteURL | Text | DEV site URL | PROD site URL |
| cst_AdminEmail | Text | dev-admin@... | it-admin@... |
| cst_TeamsChannelIT | Text | DEV channel ID | PROD channel ID |
| cst_MaxAttachmentMB | Number | 10 | 10 |

- Connection references (create for each connector):
  - Contoso SharePoint, Contoso Teams, Contoso Outlook, Contoso Dataverse

#### Step 3 — Update Flows to Use Connection References
Open each flow → replace direct connections with connection references

### Phase 2 — Source Control

#### Step 4 — Export Unmanaged Solution
Solution → **Export** → **Unmanaged** → Version 1.0.0.0 → Download zip

#### Step 5 — Unpack and Commit (using PAC CLI)
```bash
npm install -g @microsoft/powerplatform-cli
pac auth create --url https://yourorg.crm.dynamics.com
pac solution unpack --zipfile ContosoITHelpdesk.zip --folder ./ContosoITHelpdesk
git init && git add . && git commit -m "v1.0.0 Initial release"
git push origin main
```

### Phase 3 — Deployment Pipelines (Native Power Platform)

#### Step 6 — Configure Pipeline
1. Power Apps → **Deployment pipelines** → **+ New pipeline**
2. Name: `IT Helpdesk Deployment`
3. Add stages: DEV → TEST → PROD
4. For PROD: configure pre-deployment approval (IT Manager)

#### Step 7 — Deploy to TEST
1. Solutions → select solution → **Deployment pipelines**
2. Click **Deploy here** for TEST
3. Enter release notes → **Deploy**
4. Validate app in TEST environment

#### Step 8 — Deploy to PROD
1. After TEST validation → **Deploy** to PROD
2. IT Manager receives approval request
3. After approval: automated deployment runs

### Phase 4 — Monitoring

#### Step 9 — Flow Monitoring
1. Power Automate → **Monitor** → **Cloud flow activity**
2. Set failure notifications: Monitor → Settings → notification email
3. Create Power BI report: daily run counts, success rates, failure trends

#### Step 10 — App Monitoring
1. Canvas App Settings → **General** → Connect Application Insights
2. Track: screen loads, errors, session duration, user paths

---

## 📋 Course Completion

**Congratulations! You have completed all 10 days.**

**What you built across the course:**

| Day | POC Project | Key Technology |
|---|---|---|
| 1 | Invoice email flow | Power Automate + prebuilt AI |
| 2 | Custom invoice extraction | AI Builder Document Processing |
| 3 | Inventory scanner app | Object Detection + Canvas App |
| 4 | Feedback analyzer | Classification + Sentiment |
| 5 | Support ticket router | Email automation + AI routing |
| 6 | AI helpdesk app | GPT Prompt + Canvas App |
| 7 | Document automation pipeline | Multi-model enterprise workflow |
| 8 | Self-service portal | Power Pages + Dataverse |
| 9 | API connectors | Custom Connectors + external APIs |
| 10 | Deployment pipeline | ALM + Solution management |

**Next certifications to pursue:**
- PL-100: Power Platform App Maker
- PL-200: Power Platform Functional Consultant
- AI-900: Azure AI Fundamentals

*You have completed the course. Well done! 🎉*
