# Module 08 — Power Pages Development


---

## 🎯 Learning Objectives
- Understand Power Pages architecture and use cases
- Configure Dataverse integration
- Implement authentication and authorization
- Build forms and lists for external users
- Create an employee self-service portal

---

## 📖 Theory

### What is Power Pages?

External-facing websites backed by Dataverse. Unlike Power Apps (internal users with Microsoft accounts), Power Pages is for anyone — customers, partners, public, or employees accessing from outside.

> **Trainer insight:** The most common Power Pages mistake I see is treating it like a website builder. It is a Dataverse portal. Think data-first, not design-first.

### Architecture
```
External User (browser)
      ↓
Power Pages Website
      ↓ (queries/writes)
Dataverse Tables
      ↑ (synced from)
Power Apps / Power Automate (internal processes)
```

### Authentication Options

| Method | Best for |
|---|---|
| Azure AD | Employees (SSO) |
| Azure AD B2C | Customers (social login) |
| Local auth | Simple internal portals only |
| Open ID Connect | Custom identity providers |

> Never use local authentication in production. Always Azure AD for employees, Azure AD B2C for customers.

### Table Permissions (Most Critical Concept)

**Permission scopes:**
- **Global** — all records (use very carefully)
- **Contact** — only records owned by logged-in contact ← use this for customer data
- **Account** — records belonging to user's organization
- **Self** — only user's own profile

**CRUD permissions:** Read, Create, Write, Delete, Append, Append To

> Start with the most restrictive permission. A customer should ONLY see their OWN data.

### Core Components

| Component | Purpose |
|---|---|
| Pages | Individual web pages |
| Entity List (List) | Display multiple Dataverse records |
| Entity Form (Form) | Create/edit a single record |
| Web Roles | Define user types and permissions |
| Table Permissions | Control data access per role |

---

## ✅ Best Practices

1. **Plan web roles and permissions before building** — draw a role × permission matrix first
2. **Use Contact scope for customer data** — never Global scope for external portals
3. **Always test as anonymous user** — open incognito, verify unauthenticated access is blocked
4. **Use custom domain** — portal.company.com not company.powerappsportals.com
5. **Enable SSL** — Power Pages provides HTTPS by default; never disable
6. **Staged deployment** — DEV portal → TEST portal → PROD portal
7. **Monitor with Application Insights** — track page views, errors, user journeys

---

## 🔬 Hands-On Lab — Employee Self-Service Portal

### Scenario
Employees access a portal (from any browser) to:
- Submit IT support requests
- View status of their tickets
- Browse IT knowledge base

### Step 1 — Create the Site
1. [make.powerpages.microsoft.com](https://make.powerpages.microsoft.com)
2. **+ Create a website** → **Blank** template
3. Name: `Contoso IT Self-Service Portal`
4. Wait 2-3 min for provisioning

### Step 2 — Configure Authentication
- Security → Authentication Settings → Enable **Azure Active Directory**
- Configure your tenant's Azure AD

### Step 3 — Create Dataverse Tables
- `cr_ITTickets` (TicketNumber, Subject, Description, Category, Priority, Status, SubmittedBy, CreatedDate)
- `cr_KnowledgeBase` (Title, Content, Category, Published)

### Step 4 — Configure Web Roles

| Role | Tables | Scope | Permissions |
|---|---|---|---|
| Authenticated Users | ITTickets | Contact | Read, Create |
| Authenticated Users | KnowledgeBase | Global | Read |
| Anonymous Users | KnowledgeBase | Global | Read |

### Step 5 — Build Pages

**Page 1: Home**
- Welcome with user name: `{{ user.fullname }}`
- Buttons: Submit Ticket | My Tickets | Knowledge Base

**Page 2: Submit Ticket (Form — Insert mode)**
- Table: ITTickets
- Fields: Subject, Description, Category, Priority
- Hidden: SubmittedBy = current contact, Status = Open
- On success: Confirmation with ticket number

**Page 3: My Tickets (List)**
- Table: ITTickets
- View filtered to current contact (Contact scope enforces this)
- Columns: TicketNumber, Subject, Category, Priority, Status

**Page 4: Knowledge Base (List — anonymous access)**
- Table: KnowledgeBase
- View: Published = Yes
- Search enabled, filter by Category

### Step 6 — Connect Power Automate
Create flow: new ITTicket record → AI Builder classify → Teams notification to IT team

### Step 7 — Test
1. Incognito → can see Knowledge Base, cannot see tickets
2. Log in as employee → submit ticket → appears in My Tickets
3. Log in as different employee → cannot see first employee's tickets

---

## 📋 POC Summary

**Project:** Employee IT Self-Service Portal
**Auth:** Azure AD (SSO for employees)
**Data isolation:** Contact scope — employees see only their own tickets

*Next: [Module 09 — Custom Connectors →](../Module-09-Custom-Connectors/README.md)*
