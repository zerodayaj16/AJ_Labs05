# SentinelVault DLP

A standalone, local-first Data Loss Prevention prototype for sensitive-data discovery and incident triage.

## Features

- Scan pasted content or local JSON, CSV, TXT, and LOG files
- Detect and mask private keys, API keys, credit cards, emails, PAN, and Aadhaar-like identifiers
- Assign Critical, High, and Medium severity
- Track incidents through New, Investigating, Escalated, and Closed states
- Review policy coverage and monitored channels
- Use a synthetic sample dataset for demonstrations

All scanning happens in the browser session. This prototype does not provide endpoint enforcement or network blocking.

## Run locally

Install Node.js 22.13 or later, then run:

```bash
npm install
npm run dev
```

For a production build:

```bash
npm run build
```
