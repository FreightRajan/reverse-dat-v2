---
type: guide
title: Reverse DAT v2 quickstart
description: Route changes in the screenshot-assisted shipper research application.
tags: [repository, development]
verified:
  - by: openwiki/0.7.0
    at: 2026-10-05T03:53:51.400Z
sources:
  - id: openwiki-source-f8d10828394c4129061d5b0e
    resource: repo://index.html
generated: { by: "codex", at: "2026-10-05T03:53:51.400Z" }
---

# Reverse DAT v2 quickstart

The repository name is `reverse-dat-v2`; the application identifies itself as **v3.3**. The complete application remains in one static `index.html`, loading React 18, ReactDOM, and Babel from CDNs. Its tabs are Analyze, Log, Intel, and Settings.

For a local preview, serve the folder with `python3 -m http.server 8000` and open `http://localhost:8000`. This suggested command has no build step. The Settings UI mentions Netlify deployment, but actual hosting configuration is not contained in this two-file application.

## Capability map

| User action | Code to inspect |
|---|---|
| Enter a load or parse a screenshot by upload, drop, or paste | `LoadForm`, `handleFile`, `EMPTY_FORM` |
| Identify likely shippers with web-search assistance | `App.analyze` |
| Compare posted, spot, and contract rates | `parseRate`, `rateIntel` |
| Confirm or correct a shipper for future prompts | `ResultCard`, `IntelTab`, `buildKBContext`, `addCorrection` |
| Save a log, export TSV, or send rows/screenshots to Google | `persistLog`, `buildTSV`, `pushToSheets`, `APPS_SCRIPT_CODE` |

Read [research and intelligence](architecture/research.md) for prompt and rate changes, and [data and integrations](architecture/data-integrations.md) for storage, exports, and external effects.

## Development checks

Use disposable browser data and a test spreadsheet. Suggested smoke checks: screenshot parse success and malformed JSON; missing API key and API error; rate thresholds with synthetic inputs; log and Intel persistence after reload; clean SHIPPER_LIST prospect buttons; failed Sheets upload after successful analysis. No automated test harness is part of the original application.

The visible password gate is implemented entirely in client code and localStorage; it is not a server authentication boundary. Analysis calls can incur Anthropic charges. Saving and testing a Sheets URL sends a real row; screenshot-enabled exports can create Drive files. Keep credentials and user loads out of documentation.
