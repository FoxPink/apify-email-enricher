# Email Enricher+ — Clean, Verify & Enrich Contacts

<p align="center">
  <a href="https://apify.com/foxpink/email-enricher-plus">
    <img src="https://img.shields.io/badge/Run_on_Apify_Cloud-0.01_per_1k_results-FF7754?style=for-the-badge&logo=apify" alt="Run on Apify Cloud">
  </a>
</p>

<p align="center">
  <a href="https://apify.com/foxpink/email-enricher-plus">
    <img src="https://img.shields.io/badge/Apify-Store-FF7754?style=flat-square" alt="Apify Store">
  </a>
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="MIT">
</p>

Upload raw CSV/JSON contacts. Clean names, phones, emails. Verify deliverability with DNS MX + SPF + SMTP handshake + Catch-All detection. **Zero DOM, never breaks** — at **$0.01/1k results**.

---

## Features

- Clean & normalize name, email, phone, domain
- DNS MX lookup — verify mail server exists
- SPF record resolution — check sender policy
- SMTP handshake — confirm mailbox existence
- Catch-All detection — 2-step: fake email vs real email
- Bulk processing with configurable concurrency
- 5 API endpoints: enriched, deliverable-only, summary, CSV, JSON

**Status:** DELIVERABLE | RISKY_CATCH_ALL | UNDELIVERABLE | UNKNOWN

## Quick Start

```bash
curl -X POST https://api.apify.com/v2/acts/foxpink~email-enricher-plus/runs \
  -H "Content-Type: application/json" \
  -d '{
    "records": [{ "email": "john.doe@gmail.com", "firstName": "John" }],
    "performSmtpCheck": true,
    "detectCatchAll": true
  }' \
  "https://api.apify.com/v2/acts/foxpink~email-enricher-plus/runs?token=YOUR_API_TOKEN"
```

## Output

| Field | Type | Example |
|-------|------|---------|
| firstName | string | John |
| email | string | john.doe@gmail.com |
| status | string | DELIVERABLE |
| catchAll | boolean | false |
| mxFound | boolean | true |
| smtpValid | boolean | true |

## Pricing

**$0.01 per 1,000 results.**

---

<p align="center">
  Built by <a href="https://apify.com/foxpink">Nguyen Anh Duy</a> — FoxPink Studio
</p>
