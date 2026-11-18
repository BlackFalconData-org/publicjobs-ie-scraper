# Publicjobs Ireland Scraper

Extract structured data from [publicjobs.ie](https://publicjobs.ie) — publicjobs.ie — records. Every record carries location, organisation and closing date the source publishes. Salary is included where the source lists it. Structured rows, ready to import into a CRM or a spreadsheet.

**[Publicjobs Ireland Scraper on Apify →](https://apify.com/blackfalcondata/publicjobs-ie-scraper?fpr=1h3gvi)**

---

## 🚀 How to use this actor

> ### 💚 $5 free Apify credits — every month
> No credit card required. No commitment. Cancel anytime.

### 👉 [Sign up free on Apify →](https://console.apify.com/sign-up?fpr=1h3gvi)

1. **Click sign up** — pick GitHub, Google, or email; takes ~30 seconds
2. **Open this actor** — input is pre-filled with a working example
3. **Click Start** — export results as JSON, CSV, or Excel

Your **$5 monthly platform credit** is enough to run this actor right away — and again every month — scraping typically several hundred to several thousand results per run, depending on your input.

## Key features

**Search with filters** — Search by keyword and location. Filter by 🏷️ vacancy type, 📝 description format, and more.

**Detail enrichment** — Fetch full descriptions, salary data, direct apply URLs for each job.

**Incremental mode** — Only get new or changed jobs since your last run. Content hash per job — no duplicates, no re-processing.

**Change classification** — Track unchanged, expired, cross-run duplicate detection across runs. Build audit trails of how jobs evolve over time.

**Compact output** — Emit core fields only (AI-agent / MCP-friendly). Keeps response size small for LLM workflows.

**Description truncation** — Cap description length per ${NOUN} to control output size and cost.

**Result cap** — Stop after N jobs (up to 1.000). Set to 0 for the full catalog.

**Export anywhere** — Download as JSON, CSV, or Excel. Stream via Apify API, webhooks, or integrations with Make, Zapier, Airbyte, Keboola.

**Structured data** — Every job returns the same schema with consistent field naming. All fields always present — `null` when unavailable, never omitted.

---

## Use cases

**Data pipeline automation**
Integrate with your ETL pipeline to collect structured jobs from publicjobs.ie on a schedule. Export to CSV, JSON, or directly to your database. Use compact mode to control output size.

**Market research**
Monitor jobs, track trends, and analyze market dynamics with structured, deduplicated data from publicjobs.ie.

**Change monitoring**
Run daily or hourly in incremental mode to capture only new, updated, or expired ${NOUNS}. Perfect for price-tracking, churn analysis, and alerting pipelines.

**Compensation benchmarking**
Aggregate salary ranges across roles, industries, and locations on publicjobs.ie to inform pricing decisions, hiring plans, or candidate negotiations.

**AI / LLM training data**
Structured JSON per job is ready for RAG pipelines, embeddings, and agent workflows. Compact mode trims tokens for LLM context windows.

---

## Quick start

```json
{
  "query": "clerical officer",
  "maxResults": 100,
  "includeDetails": true,
  "descriptionMaxLength": 0,
  "compact": false,
  "descriptionFormat": "all",
  "excludeEmptyFields": false,
  "incrementalMode": false,
  "emitUnchanged": false,
  "emitExpired": false,
  "skipReposts": false,
  "notifyOnlyChanges": false,
  "notificationLimit": 5
}
```

---

## Input parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | string | `""` | Keywords to search the board for, e.g. "clerical officer", "engineer", "nurse". Use a JSON array (["clerical officer","executive officer"]) to run several searches in one run. Leave empty (and give no start URLs) to list every open vacancy on the board. To narrow by place, use the 📍 Location field. |
| `location` | string | — | Only keep vacancies whose location or county contains this text, e.g. "Cork", "Galway", "Dublin". Not case sensitive. The board has no location search of its own, so the filter is applied to each vacancy as it is read; the county is only known when "Include vacancy details" is on. Leave empty for the whole country. |
| `vacancyType` | enum | `""` | Only keep vacancies of this type: "publicjobs" (advertised by the Public Appointments Service) or "External Recruiter" (advertised on behalf of another body). Not case sensitive. Leave on All for every type. |
| `daysOld` | integer | `0` | Only keep vacancies advertised within the last N days (measured from the run's start). 0 or empty = any age. A vacancy whose advertising date is missing or unreadable is kept. |
| `category` | string | — | Only keep vacancies whose title, role type, grade, vacancy type or organisation contains this text, e.g. "clerical", "engineer", "Revenue". Not case sensitive. Role type and grade come from the vacancy page, so they are only matched when "Include vacancy details" is on; otherwise only title, vacancy type and organisation are searched. |
| `maxResults` | integer | `100` | Maximum total vacancies to deliver (0 = every match). The whole board carries a few hundred live vacancies at a time. |
| `startUrls` | array | `[]` | Paste search URLs from the board. Each URL becomes its own search task; results are merged and deduped by vacancy ID across all URLs. When provided, startUrls REPLACE the search terms (unsupported URLs are skipped, and if none is supported the run stops with an input error). |
| `includeDetails` | boolean | `true` | Open each vacancy page to add grade, contract and role type, working pattern, county, the full vacancy text and its attached documents. Off = search-results fields only (title, organisation, location, vacancy type, dates, link). |
| `descriptionMaxLength` | integer | `0` | Truncate the vacancy text to N characters (applies to every text format). 0 = keep the full text. |
| `compact` | boolean | `false` | Core fields only — identity, title, organisation, location/county, grade, contract and role type, dates and the link (for AI-agent/MCP workflows). |
| `descriptionFormat` | enum | `"all"` | Which representations of the vacancy text to emit. `all` keeps description + descriptionText + descriptionHtml + descriptionMarkdown (default). `text` / `html` / `markdown` keep only one variant — typically halves output size. |
| `excludeEmptyFields` | boolean | `false` | Drop null / empty-string / empty-array fields (and fully-null nested objects like socialProfiles) from each output record. Off by default to preserve dataset schema compatibility; turn on to slim output for LLM / agent consumption. |
| `incrementalMode` | boolean | `false` | Compare against the previous run and return only new, updated (and optionally expired) vacancies. stateKey is optional — it defaults to a value derived from the search terms / start URLs, so different searches never share state. |
| `stateKey` | string | — | Optional. Stable identifier for the tracked search universe. Leave empty to auto-generate from search inputs. |
| `emitUnchanged` | boolean | `false` | Also emit vacancies that have not changed since the last run (incrementalMode only). |
| `emitExpired` | boolean | `false` | Also emit vacancies that have disappeared since the last run — closed or withdrawn (incrementalMode only). |
| `skipReposts` | boolean | `false` | When incremental, skip vacancies whose content matches one that expired in a prior run (the board re-advertises competitions). |
| `notifyOnlyChanges` | boolean | `false` | When Incremental Mode is on, only send notifications for NEW and UPDATED vacancies. Has no effect outside incremental mode. |
| `notificationLimit` | integer | `5` | Maximum number of vacancies included in each notification message (1–20). |
| `telegramToken` | string | — | Telegram bot token (from @BotFather). Required for Telegram notifications. |
| `telegramChatId` | string | — | Telegram chat or channel ID (e.g. "-100123456789"). Required when telegramToken is set. |
| `discordWebhookUrl` | string | — | Discord incoming webhook URL. Server Settings → Integrations → Webhooks → New Webhook. |
| `slackWebhookUrl` | string | — | Slack incoming webhook URL. api.slack.com/messaging/webhooks. |
| `whatsappPhoneNumberId` | string | — | Your WhatsApp Business phone-number ID (numeric, from Meta dashboard). Required when whatsappAccessToken is set. |
| `whatsappAccessToken` | string | — | WhatsApp Cloud API permanent access token (System User token from Meta Business). Recipient must have messaged the business number within the last 24h (service-conversation window — free since Nov 2024). |
| `whatsappTo` | string | — | Recipient phone in E.164 format without + (e.g. "436641234567"). Recipient must have messaged your business number within last 24h. |
| `webhookUrl` | string | — | Receives a JSON POST with {metadata, items} after each run. Universal escape hatch for n8n / Make / Zapier / custom backends. |
| `webhookHeaders` | object | — | Optional JSON object of custom headers (e.g. {"Authorization":"Bearer ..."}). |
| `appConnector` | string | — | Optional. Pick a connected app under Settings → API & Integrations to receive your results (including any contact details). Best-effort across MCP connectors as Apify expands its catalog. |
| `mcpIssueTeam` | string | — | Only when the connected app is an issue tracker: the team (name or ID) the summary issue is created under, if that app requires one. |

---

## Output fields

Every job returns the same 55-field schema. Missing values are `null` — never omitted.

- `jobId`
- `vacancyId`
- `title`
- `organisation`
- `company`
- `organisationWebsite`
- `orgWebsite`
- `location`
- `county`
- `latitude`
- `longitude`
- `vacancyType`
- `grade`
- `contractType`
- `contract`
- `roleType`
- `workingPattern`
- `advertisingDate`
- `publishDate`
- `publishDateISO`
- `originalPublishDate`
- `closingDate`
- `summary`
- `description`
- `descriptionText`
- `descriptionHtml`
- `descriptionMarkdown`
- `documents`
- `salaryMin`
- `salaryMax`
- `salaryCurrency`
- `salaryType`
- `extractedEmails`
- `extractedPhones`
- `extractedUrls`
- `socialProfiles`
- `url`
- `portalUrl`
- `applyUrl`
- `originalUrl`
- `searchQuery`
- `contentQuality`
- `detailFetched`
- `scrapedAt`
- `source`
- `contentHash`
- `changeStatus`
- `isRepost`
- `repostOfId`
- `repostDetectedAt`
- `changeType`
- `firstSeenAt`
- `lastSeenAt`
- `previousSeenAt`
- `expiredAt`

---

## Sample output

One object per job. Here is a real example from a production run:

```json
{
  "jobId": "71758d664bec7217c5c6aeeefbf4fd95a610832fc331e287956ef2e96a5fb688",
  "vacancyId": "10507",
  "title": "Temporary Clerical Officer in the Civil and Public Service 2027 Nationwide",
  "organisation": "Civil Service",
  "company": "Civil Service",
  "organisationWebsite": "https://www.gov.ie/en/departments/",
  "orgWebsite": "https://www.gov.ie/en/departments/",
  "location": "Carlow, Cavan, Clare, Cork, Donegal, Dublin, Galway, Kerry, Kildare, Kilkenny, Laois, Leitrim, Limerick, Longford, Louth, Mayo, Meath, Monaghan, Offaly, Roscommon, Sligo, Tipperary…",
  "county": "Carlow, Cavan, Clare, Cork, Donegal, Dublin, Galway, Kerry, Kildare, Kilkenny, Laois, Leitrim, Limerick, Longford, Louth, Mayo, Meath, Monaghan, Offaly, Roscommon, Sligo, Tipperary…",
  "latitude": null,
  "longitude": null,
  "vacancyType": "publicjobs"
}
```

*Truncated — full records contain 55 fields. See Output fields for the complete schema.*

**[Try Publicjobs Ireland Scraper now — $5 free credit, no credit card →](https://apify.com/blackfalcondata/publicjobs-ie-scraper?fpr=1h3gvi)**

---

## Pricing

Pay only for what you extract. No subscription required — Apify's free $5 credit covers thousands of results.

| Event | Price (USD) |
| --- | --- |
| Actor Start | $0.01 |
| Result | $0.00135 |

See the [actor on Apify](https://apify.com/blackfalcondata/publicjobs-ie-scraper?fpr=1h3gvi) for current pricing.

---

## FAQ

**How do I scrape publicjobs.ie?**
Use this actor on Apify to extract structured data from publicjobs.ie. Configure your search query and filters in the input, then click Start — no coding required.

**How do I get publicjobs.ie data as JSON, CSV, or Excel?**
The actor writes each job to Apify's dataset. Download as JSON, CSV, or Excel from the Console, stream via the API, or push to Make, Zapier, Airbyte, or Keboola.

**Is it legal to scrape publicjobs.ie?**
Web scraping of publicly available data is generally legal. This actor only accesses publicly visible information. Always check publicjobs.ie's terms of service for your specific use case.

**How much does it cost?**
Pay-per-event pricing — you only pay for ${NOUNS} extracted. Apify's free $5 credit is enough to run thousands of results before you pay anything.

**How does incremental mode work?**
Each job gets a content hash. On subsequent runs, only new or changed jobs are emitted — saving time, compute, and storage. Expired jobs can be tracked separately.

**Do I need an API key or credentials?**
No. Just sign up for Apify, paste your input, and click Start. No credit card required.

---

## Related products by Black Falcon Data

- [Djinni Scraper](https://apify.com/blackfalcondata/djinni-scraper?fpr=1h3gvi) — Ukraine & CEE tech jobs with salary and English level
- [Work.ua Scraper](https://apify.com/blackfalcondata/work-ua-scraper?fpr=1h3gvi) — Ukraine's work.ua job listings
- [NoFluffJobs Scraper](https://apify.com/blackfalcondata/nofluffjobs-scraper?fpr=1h3gvi) — Poland & CEE IT jobs with transparent salary ranges
- [OLX Jobs Scraper](https://apify.com/blackfalcondata/olx-jobs-scraper?fpr=1h3gvi) — OLX job listings across 7 CEE countries
- [Prace.cz Scraper](https://apify.com/blackfalcondata/prace-scraper?fpr=1h3gvi) — Czech Republic's prace.cz job board
- [Profession.hu Scraper](https://apify.com/blackfalcondata/profession-scraper?fpr=1h3gvi) — Hungary's profession.hu job board

[Browse all Black Falcon Data actors →](https://apify.com/blackfalcondata?fpr=1h3gvi)

---

## Getting started with Apify

New to Apify? [Create a free account with $5 credit](https://console.apify.com/sign-up?fpr=1h3gvi) — no credit card required.

1. Sign up — $5 platform credit included
2. Open [Publicjobs Ireland Scraper](https://apify.com/blackfalcondata/publicjobs-ie-scraper?fpr=1h3gvi) and configure your input
3. Click **Start** — export results as JSON, CSV, or Excel

Need more later? [See Apify pricing](https://apify.com/pricing?fpr=1h3gvi).

---

## About Black Falcon Data

Black Falcon Data builds production-grade web scrapers for job boards and marketplace data. Browse our full actor catalog at [www.blackfalcondata.com](https://www.blackfalcondata.com).

---

*Last updated: 2026 11*
