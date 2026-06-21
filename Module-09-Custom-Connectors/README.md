# Module 09 — Custom Connectors


---

## 🎯 Learning Objectives
- Understand REST APIs and OpenAPI specification
- Create custom connectors for external APIs
- Configure API Key authentication
- Build a weather and news API connector
- Use connectors in Power Apps and Power Automate

---

## 📖 Theory

### What is a Custom Connector?

A wrapper around a REST API that makes it available as a native action in Power Apps and Power Automate — just like SharePoint or Teams connectors.

**When to use:**
- API not in the 1,400+ prebuilt connectors
- Your organization has internal APIs (ERP, CRM, HR system)
- You need to expose Power Platform to other systems

> **Trainer insight:** Every enterprise I've worked with has at least 3 internal APIs not connected to Power Platform. Custom connectors for these typically deliver the highest ROI because they unlock data previously available only to developers.

### HTTP Methods
- **GET** — Retrieve data (no body)
- **POST** — Create data (with body)
- **PUT** — Replace data (with body)
- **PATCH** — Update part of data (with body)
- **DELETE** — Remove data

### Authentication Methods

| Method | Use When |
|---|---|
| No auth | Open public data |
| API Key | Most public APIs (weather, maps, news) |
| Basic Auth | Legacy APIs |
| OAuth 2.0 | Microsoft, Google, Salesforce |
| Azure AD | Internal Azure-hosted APIs |

### Creation Methods
1. **From scratch** — build manually in UI
2. **From OpenAPI file** — import Swagger JSON/YAML (preferred)
3. **From Postman collection** — import exported collection
4. **From Azure API Management** — direct import

> Always request the OpenAPI spec from the API owner first. If unavailable, use Postman to capture calls and export.

---

## ✅ Best Practices — Day 9

1. **Store API keys in environment variables** — never hardcode in connectors
2. **Test every operation** in the connector tester before using in a flow
3. **Write meaningful descriptions** for every action and parameter
4. **Handle pagination** — many APIs return max 100 records; build pagination in your flow
5. **Version your connectors** — when API version changes, version connector too
6. **Use connection references** — don't hardcode connector instances in flows
7. **Publish to organization** — share certified connectors to avoid duplication
8. **Add error policies** — configure retry behavior for transient failures

---

## 🔬 Hands-On Lab — Weather & News API Connectors

### Scenario
Build custom connectors for: (1) current weather by city, (2) latest news headlines by category — then use them in a Morning Briefing app.

### Part A: Weather API Connector

#### Step 1 — Get API Access
1. Sign up at [weatherapi.com](https://www.weatherapi.com) (free: 1M calls/month)
2. Copy your API key

#### Step 2 — Test in Browser
```
https://api.weatherapi.com/v1/current.json?key=YOUR_KEY&q=Mumbai
```
Note fields: location.name, current.temp_c, current.condition.text, current.humidity

#### Step 3 — Create Connector
1. Power Apps → **Data** → **Custom connectors** → **+ New** → **Create from blank**
2. Name: `WeatherAPI`

**General tab:**
- Scheme: HTTPS, Host: `api.weatherapi.com`, Base URL: `/v1`

**Security tab:**
- Auth type: API Key
- Parameter name: `key`, Location: Query

**Definition tab → + New action:**
- Summary: Get current weather
- Operation ID: `GetCurrentWeather`
- Click **Import from sample**:
  - Verb: GET, URL: `https://api.weatherapi.com/v1/current.json?q=Mumbai`
- Set `q` parameter: required, label "Location (city name)"
- Add default response: paste JSON from browser test

#### Step 4 — Test
1. Test tab → **+ New connection** → enter API key
2. Operation: GetCurrentWeather, q: `Mumbai`
3. Verify 200 response → **Update connector**

#### Step 5 — Use in Power Apps
```powerfx
Set(varWeather, WeatherAPI.GetCurrentWeather({q: txtCity.Text}));
// Display:
// varWeather.current.temp_c & "°C"
// varWeather.current.condition.text
```

### Part B: News API Connector

#### Step 6 — Get Access
1. Sign up at [newsapi.org](https://newsapi.org) (free: 100 req/day)
2. Copy API key

#### Step 7 — Create Connector
1. New connector → Name: `NewsAPI`
2. Host: `newsapi.org`, Base URL: `/v2`
3. Auth: API Key, parameter name: `apiKey`, Location: Query

**Action 1: Get Top Headlines**
- Operation ID: `GetTopHeadlines`
- GET `/top-headlines`
- Parameters: `country` (optional), `category` (optional: business/technology/sports), `pageSize` (default 10)

**Action 2: Search News**
- Operation ID: `SearchEverything`
- GET `/everything`
- Parameters: `q` (required), `language`, `sortBy`, `pageSize`

### Phase 2 — Morning Briefing App

#### Screen 1: Settings
- txtCity (default: user's city from profile)
- drpTopics: multiselect (Technology, Business, Finance)
- btnGetBriefing → Navigate to Screen 2

#### Screen 2: Dashboard
Weather card:
```powerfx
ClearCollect(colWeather, WeatherAPI.GetCurrentWeather({q: txtCity.Text}))
// Display: colWeather.location.name & " — " & colWeather.current.temp_c & "°C"
```

News gallery:
```powerfx
ClearCollect(colTechNews, NewsAPI.GetTopHeadlines({category:"technology", pageSize:5}).articles);
ClearCollect(colBizNews, NewsAPI.GetTopHeadlines({category:"business", pageSize:5}).articles)
```

Gallery items show: Title, description (first 100 chars), source name, published time.
Tap → `Launch(ThisItem.url)` to open full article.

---

## 📋 POC Summary

**Project:** Executive Morning Briefing App

| Connector | API | Auth | Cost |
|---|---|---|---|
| WeatherAPI | weatherapi.com | API Key | Free (1M/month) |
| NewsAPI | newsapi.org | API Key | Free (100/day) |

*Next: [Module 10 — Best Practices & ALM →](../Module-10-Best-Practices-ALM/README.md)*
