# Email Enricher+ — Clean, Verify & Enrich Contacts

<p align="center">
  <a href="https://apify.com/foxpink/email-enricher-plus">
    <img src="https://img.shields.io/badge/Run_on_Apify_Cloud-0.01_per_1k_results-FF7754?style=for-the-badge&logo=apify" alt="Run on Apify Cloud - $0.01/1k results">
  </a>
</p>

<p align="center">
  <a href="https://apify.com/foxpink/email-enricher-plus">
    <img src="https://img.shields.io/badge/Apify-Store-FF7754?style=flat-square" alt="Apify Store">
  </a>
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="MIT">
</p>

**Upload raw CSV/JSON contacts. Clean names, phones, emails. Verify email deliverability with DNS MX + SPF + SMTP handshake + Catch-All detection.**

---

## Why this Actor?

| Problem | Solution |
|---------|----------|
| Dirty contact lists with typos, bad emails | Regex-based cleaning (name, email, phone, domain) |
| Emails bounce = domain reputation destroyed | DNS MX + SPF lookup before SMTP |
| Catch-all domains trick basic verifiers | 2-step SMTP detection (fake email → real email) |
| Manual verification is slow | Bulk concurrency: 10-50 simultaneous checks |

---

## Features

- ✅ **Clean & Normalize** — Name, email, phone, domain via regex
- ✅ **DNS MX Lookup** — Verify mail server exists
- ✅ **SPF Record Resolution** — Check sender policy
- ✅ **SMTP Handshake** — Confirm mailbox existence
- ✅ **Catch-All Detection** — 2-step: fake email → real email comparison
- ✅ **Bulk Processing** — Array of contacts, configurable concurrency
- ✅ **5 API Endpoints** — Enriched, deliverable-only, summary, CSV, JSON

**Status Outputs:** `DELIVERABLE` | `RISKY_CATCH_ALL` | `UNDELIVERABLE` | `UNKNOWN`

---

## Quick Start

```bash
curl -X POST https://api.apify.com/v2/acts/foxpink~email-enricher-plus/runs \
  -H "Content-Type: application/json" \
  -d '{
    "records": [
      { "email": "john.doe@gmail.com", "firstName": "John", "lastName": "Doe" }
    ],
    "performSmtpCheck": true,
    "detectCatchAll": true
  }' \
  "https://api.apify.com/v2/acts/foxpink~email-enricher-plus/runs?token=YOUR_API_TOKEN"
```

---

## Input

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| records | array | [] | Contacts to process |
| performSmtpCheck | boolean | true | Enable SMTP handshake |
| detectCatchAll | boolean | true | Enable catch-all detection |
| smtpTimeout | integer | 10000 | SMTP timeout (ms) |
| defaultCountryCode | string | "US" | Default phone country |
| maxConcurrency | integer | 10 | Parallel verifications |

---

## Output

| Status | Meaning |
|--------|---------|
| `DELIVERABLE` | Mailbox confirmed via SMTP |
| `RISKY_CATCH_ALL` | Domain catch-all, deliverable likely but unconfirmed |
| `UNDELIVERABLE` | SMTP rejected or MX not found |
| `UNKNOWN` | Verification inconclusive |

5 endpoints: `enrichedContacts` | `deliverableOnly` | `summary` | `csvExport` | `jsonExport`

---

## Use Cases

- ✅ **Cold Outreach** — Only mail deliverable contacts
- ✅ **CRM Import** — Clean + enrich before import
- ✅ **Lead Generation** — Verify scraped emails
- ✅ **Database Hygiene** — Remove stale contacts

---

## Pricing

**$0.01 per 1,000 results.**

---

<p align="center">
  <a href="https://apify.com/foxpink/email-enricher-plus">
    <img src="https://img.shields.io/badge/▶_Run_on_Apify_Cloud-FF7754?style=for-the-badge" alt="Run on Apify Cloud">
  </a>
</p>

<p align="center">
  <sub>Built by <a href="https://apify.com/foxpink">Nguyễn Anh Duy</a> — FoxPink Studio</sub>
</p>
