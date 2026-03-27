# Veriscor — Document Forgery Detection API

[![Website](https://img.shields.io/badge/website-veriscor.io-10B981)](https://veriscor.io)
[![Docs](https://img.shields.io/badge/docs-veriscor.io%2Fdocs-10B981)](https://veriscor.io/docs)
[![API Status](https://img.shields.io/badge/API-v1-10B981)](https://veriscor.io/docs/api/verify)

POST a document image. Get a forgery probability score. $0.05/check, no contracts.

Veriscor uses AI vision models to analyze document images for signs of forgery — metadata anomalies, compression artifacts, font inconsistencies, and pixel-level manipulation. The API returns a structured score in under 3 seconds.

## Quick Start

```bash
# 1. Sign up at https://veriscor.io/auth/signup (100 free checks/month)
# 2. Create an API key at https://veriscor.io/dashboard/keys
# 3. Make your first request:

curl -X POST https://veriscor.io/api/v1/verify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "document=@passport.jpg"
```

Response:

```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "forgeryScore": 0.12,
    "confidence": "high",
    "documentType": "passport",
    "flags": [],
    "latencyMs": 2340
  }
}
```

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| [`/api/v1/verify`](https://veriscor.io/docs/api/verify) | POST | Single document verification |
| [`/api/v1/verify/batch`](https://veriscor.io/docs/api/verify-batch) | POST | Batch verification (up to 50 docs) |
| [`/api/v1/batches/:id`](https://veriscor.io/docs/api/batches) | GET | Batch status and progress |
| [`/api/v1/audit`](https://veriscor.io/docs/api/audit) | GET | Verification audit log |
| [`/api/keys`](https://veriscor.io/docs/api/keys) | GET/POST | API key management |

## Input Methods

The `/verify` endpoint accepts documents in three ways:

**Multipart upload** (recommended for files under 4.5MB):
```bash
curl -X POST https://veriscor.io/api/v1/verify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "document=@document.jpg"
```

**URL fetch** (recommended for large files up to 10MB):
```bash
curl -X POST https://veriscor.io/api/v1/verify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"documentUrl": "https://storage.example.com/document.jpg"}'
```

**Base64** (for inline embedding):
```bash
curl -X POST https://veriscor.io/api/v1/verify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"document": "data:image/jpeg;base64,/9j/4AAQ..."}'
```

## Supported Documents

- Passports
- Government-issued IDs
- Driver licenses
- I-9 employment eligibility documents
- Utility bills
- Financial statements

**Formats:** JPEG, PNG, PDF (first page)

## Authentication

All API requests require an API key via the `Authorization: Bearer` header.

- **Live keys** (`vsk_live_*`) — production requests, billed
- **Test keys** (`vsk_test_*`) — sandbox mode, returns mock scores, no billing

Create and manage keys at [veriscor.io/dashboard/keys](https://veriscor.io/dashboard/keys).

Full authentication guide: [veriscor.io/docs/authentication](https://veriscor.io/docs/authentication)

## Batch Processing

Submit up to 50 documents in a single request. Results are delivered asynchronously via HMAC-SHA256 signed webhooks.

```bash
curl -X POST https://veriscor.io/api/v1/verify/batch \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "documents": ["base64doc1", "base64doc2"],
    "callbackUrl": "https://yourapp.com/webhook",
    "callbackSecret": "your_webhook_secret_min_16_chars"
  }'
```

Full batch documentation: [veriscor.io/docs/api/verify-batch](https://veriscor.io/docs/api/verify-batch)

## Error Handling

All errors follow a consistent envelope format:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "documentUrl must be a valid URL",
    "field": "documentUrl"
  }
}
```

Common error codes: `VALIDATION_ERROR` (400), `UNAUTHORIZED` (401), `KEY_EXPIRED` (401), `TRIAL_EXHAUSTED` (402), `RATE_LIMITED` (429), `INTERNAL_ERROR` (500).

Full error reference: [veriscor.io/docs/errors](https://veriscor.io/docs/errors)

## Rate Limits

100 requests per minute per API key. Rate limit headers are included in every response:

| Header | Description |
|--------|-------------|
| `X-RateLimit-Limit` | Maximum requests per window |
| `X-RateLimit-Remaining` | Remaining requests in current window |
| `Retry-After` | Seconds until the rate limit resets (on 429) |

Full rate limit documentation: [veriscor.io/docs/rate-limits](https://veriscor.io/docs/rate-limits)

## Pricing

| Tier | Price | Included | Overage |
|------|-------|----------|---------|
| Free | $0/month | 100 checks | — |
| Starter | $49/month | 1,000 checks | $0.10/check |
| Growth | $199/month | 5,000 checks | $0.08/check |
| Pay-as-you-go | $0/month | 0 checks | $0.05/check |

No contracts. No credit card required for the free tier.

## Security

- API keys are SHA-256 hashed before storage — never stored in plaintext
- No document images stored — only SHA-256 hashes for audit trail
- Webhook payloads signed with HMAC-SHA256
- RLS (Row Level Security) on all database tables
- 90-day audit log retention
- GDPR and CCPA compliant

## Documentation

| Resource | URL |
|----------|-----|
| Website | [veriscor.io](https://veriscor.io) |
| Quick Start | [veriscor.io/docs/quickstart](https://veriscor.io/docs/quickstart) |
| API Reference | [veriscor.io/docs/api/verify](https://veriscor.io/docs/api/verify) |
| Batch API | [veriscor.io/docs/api/verify-batch](https://veriscor.io/docs/api/verify-batch) |
| Authentication | [veriscor.io/docs/authentication](https://veriscor.io/docs/authentication) |
| Webhooks | [veriscor.io/docs/webhooks](https://veriscor.io/docs/webhooks) |
| Error Codes | [veriscor.io/docs/errors](https://veriscor.io/docs/errors) |
| Rate Limits | [veriscor.io/docs/rate-limits](https://veriscor.io/docs/rate-limits) |
| I-9 Guide | [veriscor.io/docs/guides/i9-verification](https://veriscor.io/docs/guides/i9-verification) |
| Privacy Policy | [veriscor.io/privacy](https://veriscor.io/privacy) |
| Terms of Service | [veriscor.io/terms](https://veriscor.io/terms) |

## Use Cases

### I-9 Employment Verification
Use Veriscor to verify the authenticity of documents submitted as part of I-9 employment eligibility verification. [Integration guide](https://veriscor.io/docs/guides/i9-verification)

### HR Onboarding
Automate document verification in employee onboarding flows. Verify contractor IDs, work permits, and government-issued documents without redirecting users to a third-party session.

### Proptech Tenant Screening
Validate pay stubs, bank statements, and utility bills submitted during tenant applications. Catch AI-generated synthetic documents before they pass manual review.

### iGaming KYC
Run identity document checks as part of Know Your Customer compliance. Score documents programmatically and flag suspicious submissions for manual review.

## FAQ

**How accurate is the forgery detection?**
Veriscor uses AI vision models to analyze metadata, compression artifacts, font consistency, and pixel-level anomalies. The score is based on visual forensics — not database lookups.

**Do you store the documents I submit?**
No. We store a SHA-256 hash of each submitted image in the audit log — not the image itself. Images are processed in memory and discarded after scoring.

**Can I use this for I-9 employment verification?**
Yes. The documentation includes a dedicated [I-9 integration guide](https://veriscor.io/docs/guides/i9-verification). Note: Veriscor scores document authenticity — it does not verify employment eligibility status.

**What happens if I exceed my plan's check limit?**
Overage checks are billed at your plan's overage rate. You can set a monthly hard cap in the dashboard — once reached, the API returns a 429 status code instead of processing.

## Releases

See the [GitHub Releases](https://github.com/popand/veriscor/releases) page for version history.

### v0.1.0 — MVP Launch (2026-03-27)

- **Verification API** — `POST /api/v1/verify` (single) and `POST /api/v1/verify/batch` (up to 50 documents with webhook delivery)
- **AI-powered scoring** — Claude Vision analyzes metadata, compression artifacts, font consistency, and pixel-level anomalies
- **Metered billing** — Stripe integration with free tier (100 checks/month), pay-as-you-go at $0.05/check
- **Dashboard** — API key management, audit log, billing, and interactive playground
- **Documentation site** — Quickstart, API reference, authentication, webhooks, errors, rate limits, I-9 guide
- **Security** — SHA-256 key hashing, RLS on all tables, HMAC-SHA256 webhook signatures, security headers, Vercel WAF rules
- **Quality** — 119 tests (96% coverage), Lighthouse SEO 100, zero type casts, Zod validation on all inputs

## Changelog

### 2026-03-27

**Added:**
- Test suite: 119 tests across 14 files (96% statement coverage)
- Coverage thresholds enforced (80% statements/lines/functions, 75% branches)

**Changed:**
- Logger uses `console.info` for info-level output (semantic correctness)

**Removed:**
- Unused Supabase middleware helper (dead code superseded by `proxy.ts`)

### 2026-03-26

**Fixed:**
- OG image route 500 in production
- Pricing CTA color contrast (3.38:1 to 7.0:1)
- Nav glassmorphism violation (removed `backdrop-blur-sm`)
- Privacy/Terms pages missing from sitemap
- All `as any` and `as string` type casts eliminated
- Playground 413 error handling

**Added:**
- Security headers (X-Frame-Options, HSTS, Referrer-Policy, Permissions-Policy)
- Zod `.strict()` validation on verify endpoint
- Per-page OpenGraph metadata on all doc pages
- Expanded meta descriptions to 80-160 chars on 15 pages
- Vercel WAF rules (API rate limiting, auth protection, webhook validation)
- Supabase security migration (pinned search_path, service_role-only policies)

### 2026-03-24

**Added:**
- Landing page with product positioning, pricing, and FAQ
- Supabase auth (email/password + GitHub OAuth)
- Dashboard with API key management, audit log, billing, playground
- `POST /api/v1/verify` — single document verification
- `POST /api/v1/verify/batch` — batch verification with webhook delivery
- `GET /api/v1/batches/:id` — batch status
- `GET /api/v1/audit` — audit log with cursor-based pagination
- Stripe metered billing with free tier (100 checks/month)
- HMAC-SHA256 webhook signing with exponential backoff retries
- Rate limiting (100 req/min per API key)
- Documentation site with full API reference
- SEO: sitemap, robots.txt, JSON-LD, Open Graph
- Structured JSON logging

## Contact

- Website: [veriscor.io](https://veriscor.io)
- Email: [hello@veriscor.io](mailto:hello@veriscor.io)
