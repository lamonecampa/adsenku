# Adsenku — Centralized Publisher Monitoring

Responsive multi-domain monitoring dashboard for the following portfolio.

## Portfolio mapping

**AdSense account 1**
- https://www.lamone.my.id/
- https://www.surymory.my.id/
- https://www.surymory-tech.com/
- https://www.cheffood.my.id/

**AdSense account 2**
- https://www.ohmyflower.com/

## Current status

This repository currently contains a static frontend prototype using clearly illustrative demo values. It does not yet fetch real Google data. It can be deployed as a GitHub Pages site.

Features: portfolio overview, account/domain mapping, revenue/RPM views, traffic analytics layout, page performance, website health, alert acknowledgement, CSV export and responsive UI.

## Run locally

```bash
python -m http.server 8080
```
Open http://localhost:8080.

## GitHub Pages

Repository Settings → Pages → Deploy from a branch → `main` → `/ (root)`.

## Production architecture

GitHub Pages is static hosting. It cannot safely store OAuth client secrets or refresh tokens, run scheduled synchronization jobs, or host a private database. Keep the frontend here and deploy a separate backend.

Recommended:
- Frontend: GitHub Pages / static hosting
- Backend: Python + FastAPI
- Database: PostgreSQL (optionally TimescaleDB)
- Scheduler: Celery + Redis or managed scheduled jobs
- Authentication: private admin login
- Integrations: AdSense Management API v2, GA4 Data API, Search Console API

### Separate AdSense accounts

Use two independently authorized credential sets. The Google identity used for account 1 must have access to the four listed sites; account 2 must be authorized independently for ohmyflower.com. Never assume one refresh token can read both accounts.

Backend sync should preserve account ID, date, domain mapping, currency, retrieval timestamp and report parameters. Refresh estimated reports because estimated earnings can later be adjusted. Handle the two accounts independently so one expired token does not block the other.

### Environment secret names (examples only)

```env
APP_SESSION_SECRET=
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
ADSENSE_ACCOUNT_1_REFRESH_TOKEN=
ADSENSE_ACCOUNT_2_REFRESH_TOKEN=
ADSENSE_ACCOUNT_1_ID=
ADSENSE_ACCOUNT_2_ID=
DATABASE_URL=
GA4_PROPERTY_IDS=
SEARCH_CONSOLE_SITES=
FRONTEND_ORIGIN=
```

Never commit real credentials or a populated .env file.

## Data integrity

- Label estimated earnings distinctly from finalized payment information.
- Do not add different currencies without explicit conversion.
- Do not equate Page RPM, impression RPM and ad-request RPM.
- Missing rows are not automatically zero.
- Align time zones and date ranges across integrations.
- High CTR or an anomaly is not proof of invalid traffic.
- AdSense page URL and GA4 landing-page reporting may not have identical scope. Document the join method and missing data.
- Alerts are investigation prompts, not policy conclusions.

## Official documentation

- https://developers.google.com/adsense/management
- https://developers.google.com/analytics/devguides/reporting/data/v1
- https://developers.google.com/webmaster-tools/v1
