# Email Enricher+ — Clean, Verify & Enrich Contacts

[![Apify Marketplace](https://img.shields.io/badge/Apify-Marketplace-FF7754)](https://apify.com/foxpink/email-enricher-plus)
[![GitHub](https://img.shields.io/badge/GitHub-Repo-181717)](https://github.com/FoxPink/apify-email-enricher)

> Upload raw CSV/JSON contacts. Clean names, phones, emails. Verify deliverability with DNS MX + SPF + SMTP handshake + Catch-All detection. **Zero DOM, never breaks.**

---

Tired of scrapers breaking every time Google Maps or LinkedIn updates their HTML layout? This Actor does not scrape web pages. Instead, it **cleans, standardizes, and verifies your existing cold leads** using pure network logic and protocol-level verification.

Stop wasting your budget on high bounce rates that burn your cold email domains. Clean your data instantly with zero maintenance overhead.

---

## Key Features

- **Layer 1: Intelligent Data Normalization** - Standardizes first/last names, strips messy characters from phone numbers, and formats them into clean strings.
- **Layer 2: DNS MX & SPF Validation** - Automatically resolves Domain MX records to ensure the target domain can actually receive mail, and checks SPF configurations.
- **Layer 3: SMTP Deep Handshake (Port 25)** - Performs a real-time protocol-level handshake with the destination mail server to verify if the specific mailbox exists **without ever sending an actual email**.
- **Layer 3b: DNS-Verified Fallback** - When SMTP is disabled or the mail server blocks the handshake (timeout/refused), returns `DNS_VERIFIED` status — still actionable data indicating the domain accepts mail, just without mailbox-level confirmation.
- **Layer 4: Catch-All Domain Detection** - Automatically tests if a domain accepts all emails (catch-all config) by sending a random fake email first. Flags these as `RISKY_CATCH_ALL`. Toggle off via `detectCatchAll: false` for faster runs.
- **Layer 5: Pattern-Based Email Generation** - When email is missing but firstName+lastName+domain are provided, generates and tests 8 common email patterns (firstname.lastname@, f.lastname@, etc.) via SMTP handshake. Finds the working pattern automatically.
- **Deliverability Scoring** - Every record gets a 0-100 score combining syntax, MX, SMTP, role-based, disposable, and catch-all signals. Filter by `score >= 80` for high-quality leads.
- **100% Stable** - Built on pure network protocols. Since it has 0% dependency on web DOM structures, this Actor **never breaks** and requires zero maintenance.
- **Alias Detection** — detects email aliases (e.g., `john+tag@domain.com`) and flags them for review to avoid duplicate contacts.
- **Quality Scoring** — assigns a 0–100 quality score per contact based on syntax validity, MX/SPF presence, SMTP handshake result, and catch-all status.

---

## Input

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `records` | Array | required | Array of contact objects |
| `performSmtpCheck` | Boolean | `true` | Enable SMTP handshake + Catch-All detection |
| `detectCatchAll` | Boolean | `true` | Send fake email first to detect catch-all domains |
| `generatePatterns` | Boolean | `true` | Generate & test email patterns when email is missing |
| `smtpTimeout` | Integer | `8` | Seconds per SMTP connection (max 20) |
| `defaultCountryCode` | String | `"+1"` | Country code prefix for phone numbers |
| `maxConcurrency` | Integer | `5` | Parallel SMTP connections (max 20) |

Each record in `records`:

| Field | Type | Description |
|-------|------|-------------|
| `firstName` | String | (Optional) First name |
| `lastName` | String | (Optional) Last name |
| `email` | String | (Optional if firstName+lastName+domain provided) Email to verify |
| `phone` | String | (Optional) Raw phone number |
| `domain` | String | (Optional) Company domain for pattern generation (e.g. `company.com`) |

### Input Example

```json
{
  "records": [
    {
      "firstName": " John ",
      "lastName": "Doe",
      "email": "JOHN.DOE@GMAIL.COM",
      "phone": "+1 (555) 019-2834"
    }
  ],
  "performSmtpCheck": true,
  "detectCatchAll": true,
  "maxConcurrency": 5
}
```

---

## Output

The Actor returns a structured and enriched dataset:

```json
[
  {
    "firstName": "John",
    "lastName": "Doe",
    "fullName": "John Doe",
    "email": "john.doe@gmail.com",
    "domain": "gmail.com",
    "phone": "+15550192834",
    "emailSyntaxValid": true,
    "mxFound": true,
    "mxServer": "gmail-smtp-in.l.google.com",
    "spfRecord": "v=spf1 redirect=_spf.google.com",
    "smtpValid": true,
    "smtpReason": "accepted",
    "catchAll": false,
    "patternGenerated": false,
    "score": 95,
    "status": "DELIVERABLE"
  },
  {
    "firstName": "Jane",
    "lastName": "Smith",
    "email": "jane@catchall-domain.com",
    "emailSyntaxValid": true,
    "mxFound": true,
    "mxServer": "mail.catchall-domain.com",
    "smtpValid": true,
    "smtpReason": "catch-all accepted",
    "catchAll": true,
    "status": "RISKY_CATCH_ALL"
  },
  {
    "firstName": "Bob",
    "lastName": "Johnson",
    "email": "bob@tight-firewall.com",
    "emailSyntaxValid": true,
    "mxFound": true,
    "mxServer": "mx.tight-firewall.com",
    "spfRecord": "v=spf1 include:_spf.google.com ~all",
    "smtpValid": false,
    "smtpReason": "",
    "catchAll": false,
    "status": "DNS_VERIFIED"
  }
]
---

## Output Schema (API Endpoints)

This Actor exposes the following outputs in the run API response:

| Endpoint | Description |
|----------|-------------|
| `enrichedContacts` | Full dataset as JSON |
| `deliverableOnly` | Filtered: only `DELIVERABLE` contacts |
| `highQualityOnly` | Filtered: only score ≥ 80 contacts |
| `summary` | Run statistics (last item with `_summary` key) |
| `csvExport` | CSV file for Excel/CRM import |
| `jsonExport` | Formatted JSON file for pipelines |

### API Usage (Python)

```python
import requests

resp = requests.get(
    "https://api.apify.com/v2/datasets/{dataset_id}/items",
    headers={"Authorization": "Bearer YOUR_API_TOKEN"}
)
data = resp.json()
for row in data:
    print(row["email"], row["status"])
```

---

## Pricing

- **Pay per event:** $0.01 / 1,000 results. Average cost: ~$0.10 per 10,000 contacts
- **No subscription:** Pay only for what you use. Runs within your Apify platform limits
- **Enterprise:** Need custom CRM integration, dedicated proxy, or bulk discounts? Contact the developer.

---

## MCP / AI Agent Integration

This Actor supports MCP (Model Context Protocol) for AI agent use. Configure in your MCP client:

```json
{
  "mcpServers": {
    "apify": {
      "command": "npx",
      "args": ["-y", "@apify/mcp-server"],
      "env": {
        "APIFY_TOKEN": "YOUR_API_TOKEN"
      }
    }
  }
}
```

The dataset schema includes rich field descriptions so AI agents can understand and chain this Actor's output with other tools automatically.
