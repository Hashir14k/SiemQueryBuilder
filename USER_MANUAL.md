# SIEM Query Builder — User Manual

> **Version 2.0** | Fully offline · No telemetry · No data leaves your machine

---

## Table of Contents

1. [Overview](#overview)
2. [Getting Started](#getting-started)
3. [Interface Walkthrough](#interface-walkthrough)
4. [Single IOC Mode](#single-ioc-mode)
5. [Bulk IOC Mode](#bulk-ioc-mode)
6. [File Import (.txt / .csv / .pdf)](#file-import-txt--csv--pdf)
7. [IOC Types & Auto-Detect](#ioc-types--auto-detect)
8. [SIEM Platforms & Query Syntax](#siem-platforms--query-syntax)
9. [Cheat Sheet](#cheat-sheet)
10. [Copying Queries](#copying-queries)
11. [Dark / Light Theme](#dark--light-theme)
12. [PWA Support](#pwa-support)
13. [Keyboard Shortcuts](#keyboard-shortcuts)
14. [Tips & Best Practices](#tips--best-practices)
15. [Troubleshooting](#troubleshooting)

---

## Overview

The **SIEM Query Builder** is a browser-based tool that generates native SIEM queries from Indicators of Compromise (IOCs). It's built for threat hunters, incident responders, and SOC analysts who need to pivot quickly across multiple SIEM platforms.

**Key capabilities:**
- Generate queries for **7 SIEM platforms**
- Support for **6 IOC types** with auto-detection
- **Single IOC** mode for quick lookups
- **Bulk IOC** mode for hunting at scale (up to 200 IOCs)
- **File import** from `.txt`, `.csv`, and `.pdf` files
- **Defanged IOC detection** — auto-refangs `[.]` `(.)` `[at]` `(at)` `hxxp://` `[:]//` patterns
- **File extension filter** — ignores 85+ non-domain extensions (.exe, .dll, .enc, .pdf, .ps1, etc.)
- **Chunked combined queries** for efficient batch hunting
- **Per-IOC validation** with color-coded feedback
- **Built-in Cheat Sheet** — search query syntax reference for all 7 SIEM platforms
- **Typewriter heading animation** — "SIEM Query Builder" types itself out on every visit
- **Dark / Light theme** — full black + green dark mode (default)
- **PWA support** — installable on desktop and mobile
- **Zero dependencies** — single HTML file, works offline

---

## Getting Started

### Method 1: Direct Open (Recommended)
1. Download `index.html` from the repository
2. Double-click it — opens in your default browser
3. Start building queries immediately

### Method 2: Local Server
```bash
python3 -m http.server 8080
# Open http://localhost:8080 in your browser
```
Serving via HTTP also enables the **PWA install** prompt and service worker caching.

### System Requirements
- Any modern browser: Chrome 90+, Firefox 90+, Edge 90+, Safari 14+
- No server, database, or internet connection required
- Works on Windows, macOS, and Linux

---

## Interface Walkthrough

The tool is organized into **4 step cards** displayed vertically:

```
┌─────────────────────────────────────────┐
│  SIEM Query Builder    [Dark Mode] [Cheat Sheet]  │  ← Header (always visible)
├─────────────────────────────────────────┤
│  Select IOC Nature                      │
│  [Single IOC] [Bulk IOCs (up to 200)]    │
│  [Auto-Detect] [IP] [Email] [Domain]... │
├─────────────────────────────────────────┤
│  Select SIEM Platform                    │
│  [IBM QRadar] [Splunk] [RSA] ...        │
├─────────────────────────────────────────┤
│  Add IOC Value                          │
│  [Input field or textarea]              │
├─────────────────────────────────────────┤
│  Generated Queries                      │
│  [Query output with copy buttons]       │
└─────────────────────────────────────────┘
```

**Header features:**
- **Heading** — "SIEM Query Builder" in Consolas font with a typewriter animation on every visit
- **Dark Mode / Light Mode** — theme toggle button
- **Cheat Sheet** — opens an in-page search query reference for all 7 SIEM platforms

Queries regenerate **instantly** as you type, select options, or change settings — no "Generate" button needed.

---

## Single IOC Mode

Best for quick one-off lookups during an investigation.

### Steps:
1. **Select "Single IOC"** tab at the top of Select IOC Nature
2. **Choose IOC type** (or leave on Auto-Detect)
3. **Pick SIEM platforms** — check one or more (e.g., QRadar + Splunk)
4. **Enter the IOC** in the input field — the query appears in Generated Queries instantly

### Example:
| Input | Auto-Detected As | Output |
|-------|-----------------|--------|
| `192.168.1.100` | IP | QRadar AQL, Splunk SPL, etc. |
| `user@evil.com` | Email | Mail-focused queries for each SIEM |
| `d41d8cd98f00b204e9800998ecf8427e` | MD5 | Hash-based hunting queries |

### Validation Feedback:
- **Green text** — IOC is valid and ready
- **Red text** — format doesn't match the selected type
- **Auto-Detect result** — shows what type was detected

---

## Bulk IOC Mode

Best for threat intelligence feeds, multiple IOCs from a report, or batch hunting.

### Steps:
1. **Select "Bulk IOCs"** tab
2. **Choose IOC type** — Auto-Detect is recommended for mixed IOCs
3. **Pick SIEM platforms**
4. **Paste IOCs** — one per line, up to 200
5. Review the validation table and generated queries

### Validation Table:
A color-coded table appears below the textarea showing each IOC:

| # | Value | Status | Detected Type |
|---|-------|--------|---------------|
| 1 | `192.168.1.100` | 🟢 OK | IP |
| 2 | `not-an-ioc` | 🔴 Invalid | ??? |
| 3 | `evil.com` | 🟡 Unknown | ??? |

- **Green dot** — valid IOC
- **Yellow dot** — unknown (select a type manually or use Auto-Detect)
- **Red dot** — invalid format

### Auto-Deduplication

Duplicate IOCs are automatically detected and skipped:
- **Case-insensitive** — `EVIL.COM` and `evil.com` are treated as the same IOC
- **First occurrence kept** — the first instance is used, all subsequent duplicates are dropped
- **Duplicate count shown** — the hint line reports how many duplicates were skipped
- Duplicates do NOT count toward the 200-IOC limit

### Debounced Rendering

To keep the tool smooth while typing large batches:
- Bulk input waits **300ms** after the last keystroke before regenerating queries
- File import, mode switching, and SIEM selection still render instantly
- No sluggishness even at the 200-IOC maximum

### Output Sections:

#### Individual Queries
One set of queries per IOC, organized by type and SIEM. Expandable `<details>` sections keep the output organized.

#### Chunked Combined Queries
IOCs of the same type are batched together using `IN` / `OR` operators:
- Adjustable **chunk size** (5, 10, 15, 25, or all 50)
- Each chunk generates one combined query per SIEM
- Useful for running fewer, broader queries instead of many individual ones

---

## File Import (.txt / .csv / .pdf)

Instead of pasting, you can import IOCs directly from a file.

### Supported Formats:

| Format | Description |
|--------|-------------|
| `.txt` | One IOC per line |
| `.csv` | Comma-separated — first column used as IOC value |
| `.pdf` | PDF report — text is extracted and scanned for IOC patterns |

### CSV Handling:
- Multi-column CSVs: the tool extracts the **first non-empty value** from each row
- Values wrapped in quotes (`"192.168.1.100"`) are automatically cleaned
- Header rows are treated as values — **remove headers before importing** or delete them from the textarea after import

### PDF Handling:
- **Zero external dependencies** — uses the browser's built-in `DecompressionStream` API
- Extracts text from PDF content streams (supports FlateDecode compression)
- Scans extracted text for IOC-like patterns (IPs, domains, emails, hashes)
- Automatically deduplicates candidates and populates the bulk textarea
- **Browser support:** Chrome 80+, Edge 80+, Safari 16.4+, Firefox 113+
- For older browsers, `.txt` and `.csv` import still works normally
- **Image-based PDFs are not supported.** The tool extracts text from PDF content streams only. PDFs where IOCs are rendered as images, screenshots, or scanned documents cannot be read. For image-based PDFs, use OCR software to convert to text first, then import the resulting `.txt` file.

### How to Import:
1. Switch to **Bulk IOCs** mode (or the tool will switch automatically)
2. **Drag & drop** a `.txt`, `.csv`, or `.pdf` file onto the upload area
   — OR —
   Click **"Browse Files"** to select a file
3. IOCs are automatically parsed and populated
4. Review the validation table
5. A toast notification confirms how many IOCs were loaded

### After Import:
- You can still **edit** the textarea — add, remove, or fix IOCs
- Click **"Remove"** to clear the file and start over
- Max **200 IOCs** are imported (automatically capped)

---

## IOC Types & Auto-Detect

### Supported Types:

| Type | Format | Example | Pattern |
|------|--------|---------|---------|
| **IP Address** | IPv4 / CIDR | `192.168.1.100`, `10.0.0.0/24` | `x.x.x.x` with optional `/yy` |
| **Domain / URL** | FQDN or full URL | `evil.com`, `https://malware-c2.com/path` | Contains TLD |
| **Email** | Standard email address | `user@evil.com` | `name@domain.tld` |
| **MD5** | 32 hex characters | `d41d8cd98f00b204e9800998ecf8427e` | `[a-f0-9]{32}` |
| **SHA-1** | 40 hex characters | `da39a3ee5e6b4b0d3255bfef95601890afd80709` | `[a-f0-9]{40}` |
| **SHA-256** | 64 hex characters | `e3b0c44298fc1c149afbf4c8996fb...` | `[a-f0-9]{64}` |

### Auto-Detect Mode:
- **On by default** — the tool guesses the IOC type from the value
- Best for **Bulk mode** with mixed IOC types
- If detection fails, manually select a type from the chips
- Detection priority: SHA-256 → SHA-1 → MD5 → IP → Email → Domain

### Manual Type Selection:
- Click any IOC type chip to force a specific type
- The tool validates all IOCs against the selected type's pattern
- Invalid IOCs show as **red** in the validation table

### Defanged IOC Detection:
The tool automatically detects and refangs common defanging patterns used in threat reports. You can paste defanged IOCs directly — no manual cleanup needed.

| Defanged Form | Refanged To | Example Input → Output |
|---|---|---|
| `[.]` | `.` | `192[.]168[.]1[.]100` → `192.168.1.100` |
| `[.]` | `.` | `evil[.]com` → `evil.com` |
| `(.)` | `.` | `bad(.)org` → `bad.org` |
| `[at]` | `@` | `attacker[at]evil[.]com` → `attacker@evil.com` |
| `(at)` | `@` | `phish(at)bad.org` → `phish@bad.org` |
| `hxxp://` | `https://` | `hxxp://evil[.]com/malware` → `https://evil.com/malware` |
| `hxxps://` | `https://` | `hxxps://c2[.]attacker[.]com` → `https://c2.attacker.com` |
| `[:]//` | `://` | `c2[:]//beacon` → `c2://beacon` |

Defanging is detected in all modes: single IOC, bulk paste, and PDF import.

### File Extension Filtering:
The auto-detect engine ignores **85+ known file extensions** that would otherwise falsely match as domains. Values ending in `.exe`, `.dll`, `.enc`, `.scr`, `.ps1`, `.vba`, `.pdf`, `.docx`, `.zip`, `.locked`, `.rnsmwr`, and many others are flagged as **Unknown** (not Domain).

Real TLDs — `.com`, `.net`, `.org`, `.io`, `.gov`, `.mil`, `.edu` — are never filtered. The complete list is documented in `IGNORED_EXTENSIONS.txt`.

If a valid domain is incorrectly filtered, or a new file extension causes false positives, edit the `BAD_EXTS` object in `index.html`.

---

## SIEM Platforms & Query Syntax

### IBM QRadar
**Syntax:** AQL (Ariel Query Language)

Queries use `SELECT * FROM events WHERE ...` patterns with optional `LAST N DAYS` time windows. Includes separate queries for:
- Event data (`events` table)
- Flow data (`flows` table)
- Category-based filtering (mail category 16000, FIM category 7000, etc.)
- Payload search for deep inspection

### Splunk
**Syntax:** SPL (Search Processing Language)

Queries use `index=*` with field-based filters and `stats count by` aggregation. Output is sorted by count descending. Includes:
- Field-mapped searches (`src_ip`, `dest_ip`, `file_hash`, etc.)
- `stats count by index, sourcetype` for quick pivoting
- Pipe-chained commands for result refinement

### RSA NetWitness
**Syntax:** Native NetWitness query language

Queries use logical operators (`||` for OR) with specific attribute references. Includes:
- `ip.src`, `ip.dst` for IP hunting
- `file.hash`, `checksum` for hash lookups
- `alias.host`, `web.domain`, `dns.hostname` for domain/URL
- `mail.src`, `mail.dst` for email investigation

### Datadog SIEM
**Syntax:** Faceted log queries with `@attribute` notation

Queries target specific log attributes using the `@` prefix. Includes:
- `@src.ip`, `@dst.ip`, `@network.client.ip` for IP
- `@email`, `@usr.email`, `@mail.from`, `@mail.to` for email
- `@dns.question`, `@http.url`, `@http.host` for domain/URL
- `@file.hash`, `@file.hash.sha256` for hash lookups
- Service-scoped queries (`service:firewall`, `service:endpoint`, etc.)

### Wazuh
**Syntax:** Lucene / Elasticsearch field-based queries

Queries use field-based syntax compatible with the Wazuh indexer. Includes:
- `data.srcip`, `data.dstip` for IP with rule level filtering
- `data.email`, `email`, `mail.from`, `mail.to` for email
- `data.url`, `data.domain`, `url`, `domain` for domain/URL
- `file.hash`, `hash`, `sha256`, `md5` for hash lookups
- `rule.level>=10` for high-fidelity alert scoping
- `location:firewall`, `location:syscheck` for log source filtering

### Microsoft Sentinel
**Syntax:** KQL (Kusto Query Language)

Queries use `table | where Condition | project Fields | summarize` pipelines. Includes:
- `search *` for broad cross-table hunting
- `CommonSecurityLog`, `SigninLogs`, `AuditLogs` for Azure AD / firewall data
- `DeviceNetworkEvents`, `DeviceFileEvents`, `DeviceProcessEvents` for MDE endpoint data
- `EmailEvents`, `DnsEvents`, `SecurityAlert` for email, DNS, and alert investigation
- `contains`, `startswith`, `has`, `matches regex` for string operations
- `ago()`, `between`, `now()` for time filtering
- `summarize count() by`, `dcount()`, `arg_max()` for aggregation

### Elastic SIEM
**Syntax:** ECS (Elastic Common Schema) with KQL / Lucene

Queries use field-based search against ECS-normalized data. Includes:
- `source.ip`, `destination.ip`, `client.ip` for IP hunting with `event.category` scoping
- `email.sender.address`, `email.to.address` for email investigation
- `url.domain`, `dns.question.name`, `destination.domain` for domain/URL
- `file.hash.sha256`, `file.hash.md5`, `process.hash.sha256` for hash lookups
- `event.category`, `event.type`, `event.dataset` for event scoping
- `threat.indicator.*`, `threat.technique.id` for threat intel matching
- Wildcard (`*`), exact phrase (`""`), and boolean operators

---

## Cheat Sheet

The **Cheat Sheet** is an in-page search query reference covering all 7 SIEM platforms. It's accessible from the header via the **"Cheat Sheet"** button.

### Opening the Cheat Sheet
- Click **"Cheat Sheet"** in the header — the main tool cards hide and the cheat sheet appears in their place
- The header (title + theme toggle + back button) remains visible
- Click **"← Back"** (the same button toggles) to return to the main tool

### Platform Selection
A **dropdown** at the top of the cheat sheet lets you switch between platforms:
- **IBM QRadar (AQL)** — SELECT syntax, tables, operators, string matching, functions, time, aggregation, grouping
- **Splunk (SPL)** — search basics, time modifiers, pipe commands, stats/eval functions, subsearches
- **RSA NetWitness** — native syntax, operators, attributes by category (IP/port/domain/email/file/user/process)
- **Datadog SIEM** — faceted syntax, @attribute reference, service/source scoping
- **Wazuh** — Lucene syntax, operators (incl. fuzzy, regex, proximity), rule-level filtering, location scoping
- **Microsoft Sentinel (KQL)** — Kusto syntax, tables reference, string/time operators, aggregation, joins
- **Elastic SIEM** — ECS field reference, KQL/Lucene operators, event categories, aggregation, threat intel matching

### Content
Each platform cheat sheet includes:
- Query syntax basics and operators
- Key field/attribute references for threat hunting
- String matching, regex, and wildcard patterns
- Time and date handling
- Aggregation and statistical functions
- Ready-to-use threat hunting query examples

---

## Copying Queries

### Individual Copy
- Hover over any query box — a **"Copy"** button appears in the top-right corner
- Click to copy that specific query
- The button briefly shows **"Copied!"** on success

### Copy All
- Click the **"Copy All"** button in the Generated Queries header
- Copies all generated queries from all SIEMs at once
- Queries are separated by blank lines for easy pasting

### Keyboard Shortcut
- **`Ctrl + Enter`** (Windows/Linux) or **`Cmd + Enter`** (macOS)
- Copies all generated queries at once — fastest workflow for hunters

---

## Dark / Light Theme

The theme toggle button is in the top-right area of the header.

| Theme | Background | Accent | Best For |
|-------|-----------|--------|----------|
| **Dark** (default) | Pure black (`#000`) | Light green | Late-night hunting, reducing eye strain |
| **Light** | White/gray | Blue | Daytime, well-lit environments |

- Your preference is saved to **localStorage** and persists across sessions
- If no preference is saved, the tool defaults to **dark mode**
- All colors, borders, shadows, and code blocks adapt to the selected theme

---

## PWA Support

The tool can be installed as a Progressive Web App for a native-like experience.

### Features:
- **Installable** — "Add to Home Screen" / "Install app" prompt in supported browsers
- **Standalone window** — no browser chrome, looks like a native desktop app
- **Service worker** — caches the tool for reliable offline use (when served via HTTP)
- **Custom icon** — "SIEM QB" branding in green-on-black

### How to Install:
1. Serve the tool via a local HTTP server (`python3 -m http.server 8080`)
2. Open `http://localhost:8080` in Chrome or Edge
3. Click the install icon in the address bar, or use the browser menu → "Install SIEM Query Builder"

The tool still works perfectly without installation — just open `index.html` directly.

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl + Enter` / `Cmd + Enter` | Copy all generated queries |
| `Escape` | Close Cheat Sheet (legacy support) |
| `Tab` / `Shift + Tab` | Navigate between form elements |

---

## Tips & Best Practices

### Query Efficiency
- **Use Chunked Queries** for large batches — running 1 query of 10 IOCs is faster than 10 individual queries
- **Adjust chunk size** based on your SIEM's query limits — start with 10 and increase if your platform supports it
- **Select only the SIEMs you need** — generating queries for all 7 when you only use Splunk creates unnecessary output

### Cheat Sheet Usage
- Keep the **Cheat Sheet** open in a separate tab/window for quick syntax reference while hunting
- Switch between platform tabs to compare query syntax across SIEMs
- Use the threat hunting examples as templates — copy, paste, and customize

### IOC Hygiene
- **Deduplication is automatic** — the tool skips case-insensitive duplicates and reports the count
- **Validate manually** — auto-detection is pattern-based; a valid-looking IP could still be benign
- **Remove headers from CSVs** before importing

### Hunting Workflow
1. Start with **Auto-Detect** to understand what types of IOCs you have
2. Switch to a **specific type** for validation and cleaner output
3. Use **Chunked Combined Queries** for the actual hunt
4. Reference the **Cheat Sheet** for platform-specific query syntax
5. Copy individual queries when you need to tune or share a specific search

### Security
- The tool runs **entirely in your browser** — no IOC ever leaves your machine
- Safe to use on **air-gapped** workstations
- No cookies, analytics, or tracking of any kind
- Verify queries before running them in production SIEM

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| **Queries not appearing** | Check that at least one SIEM platform is selected and the IOC is valid |
| **"Could not detect IOC type"** | Select the IOC type manually by clicking one of the type chips |
| **Too many invalid IOCs** | Check for extra spaces, quotes, or non-standard formats in your input |
| **File won't import** | Ensure it's `.txt`, `.csv`, or `.pdf` — other formats are not supported |
| **Safari: Browse Files does nothing** | Hard refresh (`Cmd+Option+R`). Quit and reopen Safari if it persists. The tool uses an opacity-based hidden input pattern that requires a clean page load in Safari. |
| **PDF import fails** | PDF may use unsupported compression. Try a different PDF reader to re-export as text, or use `.txt` import instead |
| **PDF shows 0 IOCs** | PDF may contain only images/scans with no embedded text. Use OCR software to convert to `.txt` first. The tool cannot read text from images inside PDFs. |
| **Domain flagged as Unknown (yellow)** | Value ends in a known file extension — correctly filtered. See `IGNORED_EXTENSIONS.txt` for the full list. If it is a legitimate domain, manually select the Domain IOC type chip to override. |
| **CSV import picks wrong column** | Reorder your CSV so the IOC column is first, or paste directly into the textarea |
| **Dark mode not saving** | Check that localStorage is not blocked (some private/incognito modes restrict it) |
| **Copy not working** | Older browsers may not support the Clipboard API — try `Ctrl+C` on the query text instead |
| **Cheat Sheet not loading** | Ensure the HTML file is intact — the cheat sheet is built into the same file |
| **Queries not updating while typing** | Bulk mode uses a 300ms debounce — queries regenerate when you stop typing. This is normal. |
| **IOCs missing from output** | Check for duplicates — the tool skips duplicate IOCs (case-insensitive). The hint line shows how many were skipped. |
| **PWA install not showing** | Must be served via HTTP — `python3 -m http.server 8080` then open `localhost:8080` |
| **Page looks broken** | Clear browser cache, ensure JavaScript is enabled, use a modern browser |

---

## Repository Structure

```
siem-query-builder/
├── index.html                  # The tool (open this in your browser)
├── manifest.json               # PWA manifest for installable app
├── sw.js                       # Service worker for offline caching
├── README.md                   # Project overview and feature list
├── USER_MANUAL.md              # This guide
├── IGNORED_EXTENSIONS.txt      # Full list of filtered file extensions
├── LICENSE                     # MIT License
└── .gitignore
```

---

## Feedback & Contributions

Found a bug? Have a feature request? Want to add another SIEM platform?

- Open an issue on GitHub
- Submit a pull request
- Share with the threat hunting community

Built by [Hashir14k](https://github.com/Hashir14k) — for hunters, by a hunter.
