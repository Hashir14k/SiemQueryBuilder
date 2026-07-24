# SIEM Query Builder v2.0

**Offline SIEM query builder for threat hunters, by a threat hunter.**

A lightweight, browser-based tool that generates native SIEM queries from IOCs — no backend, no tracking, no data leaves your machine.

![Platform](https://img.shields.io/badge/platform-web%20%7C%20offline-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![SIEMs](https://img.shields.io/badge/SIEMs-QRadar%20%7C%20Splunk%20%7C%20RSA%20%7C%20Datadog%20%7C%20Wazuh%20%7C%20Sentinel%20%7C%20Elastic-orange)
![Version](https://img.shields.io/badge/version-2.0-informational)
![PWA](https://img.shields.io/badge/PWA-ready-success)

## Features

- **Single IOC Mode** — Enter one IOC and get formatted queries instantly
- **Bulk IOC Mode** — Paste up to 200 IOCs (one per line) with auto-deduplication and generate queries in batches
- **File Import** — Upload .txt, .csv, or .pdf files to auto-import IOCs (drag & drop or browse)
- **Auto-Detect** — Automatically identifies IPs, domains, emails, SHA-1, SHA-256, and MD5 hashes
- **Seven SIEM Platforms** — IBM QRadar (AQL), Splunk (SPL), RSA NetWitness, Datadog SIEM, Wazuh, Microsoft Sentinel (KQL), Elastic SIEM
- **Chunked Combined Queries** — Batch IOCs into `IN` / `OR` grouped queries with configurable chunk sizes
- **Per-IOC Validation** — Color-coded validation table shows which IOCs are valid/invalid/unknown
- **Auto-Deduplication** — Case-insensitive duplicate detection skips repeated IOCs and reports the count
- **Debounced Rendering** — Bulk input waits 300ms after typing before regenerating queries for smooth performance
- **Individual + Combined Output** — Both individual per-IOC queries and chunked combined queries in one view
- **Built-in Cheat Sheet** — In-page search query syntax reference for all 7 SIEM platforms with platform dropdown
- **Typewriter Animation** — "SIEM Query Builder" heading types itself out on every visit
- **Dark / Light Mode** — Full black + light green dark theme (default), clean light theme
- **PWA Support** — Installable on desktop and mobile for a native app experience
- **Copy to Clipboard** — One-click copy for individual queries, sections, or all queries at once
- **Fully Offline** — Single HTML file with zero dependencies. No server, no telemetry, no network calls

## Supported IOC Types

| Type | Format | Example |
|------|--------|---------|
| IP Address | IPv4 / CIDR | `192.168.1.100`, `10.0.0.0/24` |
| Domain / URL | FQDN or full URL | `evil.com`, `https://malware-c2.com/beacon` |
| Email | Standard email | `user@evil.com` |
| SHA-1 | 40 hex chars | `da39a3ee5e6b4b0d3255bfef95601890afd80709` |
| SHA-256 | 64 hex chars | `e3b0c44298fc1c149afbf4c8996fb924...` |
| MD5 | 32 hex chars | `d41d8cd98f00b204e9800998ecf8427e` |

## Supported SIEM Platforms

- **IBM QRadar** — AQL queries with event, flow, and category-based hunting
- **Splunk** — SPL queries with `stats count by index, sourcetype` aggregation
- **RSA NetWitness** — Native query syntax for packets, logs, and endpoint data
- **Datadog SIEM** — Faceted log queries with `@attribute` syntax, service/source scoping
- **Wazuh** — Lucene-based queries for Elasticsearch backend with rule level filtering
- **Microsoft Sentinel** — KQL (Kusto Query Language) queries across Security, Signin, Audit, and Defender tables
- **Elastic SIEM** — ECS-based queries with KQL/Lucene syntax for Elastic Security

## Documentation

See the **[User Manual](USER_MANUAL.md)** for a complete guide covering all features, SIEM syntax details, the built-in Cheat Sheet, PWA install instructions, workflows, and troubleshooting.

## Usage

### Method 1: Direct Open
1. Download `index.html`
2. Open it in any modern browser (Chrome, Firefox, Edge, Safari)
3. Start building queries — nothing else needed

### Method 2: Local Server (PWA-enabled)
```bash
python3 -m http.server 8080
# Then open http://localhost:8080
```
Serving via HTTP enables the PWA install prompt and service worker caching.

### Quick Start
1. **Select mode** — Single IOC or Bulk IOCs
2. **Choose IOC type** — or leave on Auto-Detect
3. **Pick SIEM platforms** — QRadar, Splunk, RSA NetWitness, Datadog SIEM, Wazuh, Sentinel, Elastic (any combination)
4. **Enter your IOC(s)** — type, paste, or upload a .txt/.csv/.pdf file — queries generate in real-time
5. **Copy and hunt** — click Copy buttons or press `Ctrl+Enter` to copy all
6. **Cheat Sheet** — click the Cheat Sheet button in the header for search query syntax reference

## Why Offline?

As a threat hunter, you often work with sensitive IOCs from active investigations. This tool:
- Never sends your IOCs over the network
- Runs entirely in your browser
- Has no analytics, ads, or tracking
- Can be used from an air-gapped workstation

## Contributing

Pull requests are welcome. Areas for contribution:

- Additional SIEM platforms (ArcSight, Sumo Logic, Devo, etc.)
- New IOC types (CVE, registry keys, file paths, etc.)
- Query template improvements and optimization
- Cheat Sheet expansions and additional hunting recipes
- UI/UX enhancements

## License

MIT — see [LICENSE](LICENSE) file for details.

## Author

Built by [Hashir14k](https://github.com/Hashir14k) — for the threat hunting community.
