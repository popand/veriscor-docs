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

## Contact

- Website: [veriscor.io](https://veriscor.io)
- Email: [hello@veriscor.io](mailto:hello@veriscor.io)
