# Email Enricher+ — Clean, Verify & Enrich Contacts

[![Apify Marketplace](https://img.shields.io/badge/Apify-Marketplace-FF7754)](https://apify.com/foxpink/email-enricher-plus)
[![GitHub](https://img.shields.io/badge/GitHub-Repo-181717)](https://github.com/FoxPink/apify-email-enricher)

> Upload raw CSV/JSON contacts. Clean names, phones, emails. Verify deliverability with DNS MX + SPF + SMTP handshake + Catch-All detection. **Typo detection + spam trap filtering included.**

---

## Key Features

- **Layer 1: Intelligent Data Normalization** — standardizes names, formats phones, normalizes Gmail (dot-stripping)
- **Layer 2: DNS MX & SPF Validation** — resolves MX records, checks SPF policies
- **Layer 3: SMTP Deep Handshake (Port 25)** — protocol-level mailbox verification without sending email
- **Layer 3b: DNS-Verified Fallback** — returns `DNS_VERIFIED` when SMTP blocked or disabled
- **Layer 4: Catch-All Domain Detection** — detects domains accepting all emails, flags as `RISKY_CATCH_ALL`
- **Layer 5: Pattern-Based Email Generation** — generates & tests 8+ email patterns from name+domain (works with and without SMTP)
- **Typo Suggestion (new)** — detects email domain typos (e.g., `gamil.com` → `gmail.com`) using Levenshtein distance against 100+ providers
- **Spam Trap Detection (new)** — flags honeypot/spam trap addresses with quality score penalty
- **Alias Detection** — detects plus-tags, dot-separated aliases
- **Deliverability Scoring** — 0-100 score combining syntax, MX, SMTP, role-based, disposable, catch-all, and spam trap signals

---

## Input

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `records` | Array | required | Array of contact objects |
| `performSmtpCheck` | Boolean | `true` | Enable SMTP handshake + Catch-All detection |
| `detectCatchAll` | Boolean | `true` | Send fake email first to detect catch-all domains |
| `generatePatterns` | Boolean | `true` | Generate & test email patterns when email is missing (works without SMTP too) |
| `smtpTimeout` | Integer | `8` | Seconds per SMTP connection (max 20) |
| `defaultCountryCode` | String | `"+1"` | Country code prefix for phone numbers |
| `maxConcurrency` | Integer | `5` | Parallel SMTP connections (max 20) |

Each record:

| Field | Type | Description |
|-------|------|-------------|
| `firstName` | String | (Optional) First name |
| `lastName` | String | (Optional) Last name |
| `email` | String | Email to verify (optional if name+domain provided) |
| `phone` | String | (Optional) Raw phone number |
| `domain` | String | (Optional) Domain for pattern generation |

---

## Output Fields

| Field | Description |
|-------|-------------|
| `email` | Cleaned, normalized email (Gmail dots stripped) |
| `firstName`, `lastName`, `fullName` | Standardized name fields |
| `phone` | Formatted phone with country code |
| `emailSyntaxValid` | Regex validation |
| `mxFound`, `mxServer`, `spfRecord` | DNS verification results |
| `smtpValid`, `smtpReason` | SMTP handshake result |
| `catchAll` | Catch-all domain flag |
| `isRoleBased`, `isDisposable`, `isFreeProvider` | Email classification |
| `provider` | Detected email provider |
| `patternGenerated` | Whether email was generated from name+domain |
| **`typoSuggestion` (new)** | Suggested correction for common domain typos |
| **`isSpamTrap` (new)** | Spam trap / honeypot detection |
| `aliasType` | plus-tag, dot-separated, hyphen-separated, etc. |
| `score` | 0-100 deliverability score |
| `qualityBreakdown` | 8 sub-scores: syntax, mx, smtp, roleBased, disposable, freeProvider, catchAll, spamTrap |
| `status` | DELIVERABLE / DNS_VERIFIED / RISKY_CATCH_ALL / UNDELIVERABLE / UNKNOWN |

---

## Pricing

**$0.01 / 1,000 results.** Average cost: ~$0.10 per 10,000 contacts. No subscription.

---

## MCP / AI Agent Integration

```json
{
  "mcpServers": {
    "apify": {
      "command": "npx",
      "args": ["-y", "@apify/mcp-server"],
      "env": { "APIFY_TOKEN": "YOUR_API_TOKEN" }
    }
  }
}
```

---

## Compatibility

- 100% Node.js (18+)
- No browser, no headless, no DOM
- Pure network protocols — never breaks
