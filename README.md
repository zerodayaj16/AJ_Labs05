# Evidence Terminal — Log Analyzer

A single-file, client-side security log triage tool. Paste or upload logs, get automatic severity classification and MITRE ATT&CK-style tactic tagging, and explore the results in a searchable table or an analytics dashboard — all in the browser, with nothing sent to a server.

## Features

- **Flexible ingestion** — accepts a JSON array of log objects, CSV with headers, or plain text (one entry per line). Upload files (`.json`, `.csv`, `.log`, `.txt`) or paste directly.
- **Automatic classification** — an ordered keyword ruleset assigns each entry a severity (`critical` / `high` / `medium` / `low`) and, where applicable, a MITRE ATT&CK-style tactic and technique ID (e.g. repeated failed logins → `Credential Access` / `T1110`).
- **Log Tape view** — a sortable, searchable, filterable table of every entry with severity chips and tactic tags.
- **Evidence panel** — click any row for a full breakdown: timestamp, user/actor, host/IP, message, and the raw parsed object.
- **Analytics dashboard** — severity breakdown, detected tactics, an event timeline, and top actors/hosts, rendered with Chart.js.
- **Local-only session** — no backend, no network calls beyond loading Chart.js from a CDN. Data lives only in the browser tab and clears on refresh or via the "Clear Session" button.

## Getting started

No build step or dependencies to install.

1. Download `log-analyzer.html`.
2. Open it in any modern browser.
3. Click **Load Sample Tape** to see it in action, or **Upload File** / paste your own logs to analyze real data.

## How classification works

Each entry's message (plus its raw JSON) is scanned against an ordered list of rules; the first matching rule wins:

| Severity | Example tactic | Example triggers |
|---|---|---|
| Critical | Impact, C2, Exfiltration | ransomware, backdoor, data exfiltration |
| High | Credential Access, Lateral Movement, Initial Access | brute force, pass-the-hash, phishing |
| Medium | Persistence, Discovery, Execution | scheduled task created, port scan, PowerShell |
| Low | — | anything that doesn't match a rule above |

If an uploaded entry already includes a `severity` or `level` field, that value takes precedence over the inferred one.

## Accepted input formats

- **JSON array**: `[{"timestamp": "...", "user": "...", "message": "..."}]`
- **CSV**: first row as headers, one entry per subsequent row
- **Plain text**: one log line per row; a leading `YYYY-MM-DD HH:MM:SS` timestamp is auto-detected

## Known limitations

- CSV parsing is a simple comma split and does not handle quoted fields containing commas.
- The event timeline buckets by hour-of-day, so multi-day tapes will collapse into the same hourly buckets.
- No pagination — very large tapes (many thousands of entries) may slow down search/filter re-rendering.

## Roadmap ideas

- CSV export of filtered/search results
- Quote-aware CSV parser
- Multi-day-aware timeline bucketing
- Debounced search input

## License

Add a license of your choice (MIT is a common default for lab/portfolio projects).
