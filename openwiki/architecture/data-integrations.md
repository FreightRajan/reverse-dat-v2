---
type: guide
title: Data and integration contracts
description: Map browser persistence, Sheets columns, screenshot handling, and external side effects.
tags: [repository, development]
verified:
  - by: openwiki/0.7.0
    at: 2026-10-05T03:53:51.400Z
sources:
  - id: openwiki-source-f8d10828394c4129061d5b0e
    resource: repo://index.html
generated: { by: "codex", at: "2026-10-05T03:53:51.400Z" }
---

# Data and integration contracts

## Browser data and credentials

The application uses localStorage keys `rdat_log`, `rdat_apikey`, `rdat_webapp_url`, `rdat_knowledge_base`, and `rdat_auth`. Logs contain load inputs, rate calculations, result text, timestamps, and a `hasScreenshot` flag. Screenshot bytes are excluded from local history; they are sent only with the immediate Sheets push. Consequently, a later bulk push cannot recreate the original screenshot upload from history alone.

Both Anthropic calls use the key directly from browser JavaScript. `ResultCard` uses regex formatting followed by `dangerouslySetInnerHTML`, without a sanitizer in that path. Changes to model text rendering or credential handling must account for this shared client environment.

## Sheets and Drive flow

`pushToSheets` sends JSON using a text/plain POST. The embedded Apps Script appends a row to the active spreadsheet and optionally saves a base64 screenshot into a timestamped subfolder of **Reverse DAT Screenshots**. It sets created files to anyone-with-link viewing. That sharing policy is actual script behavior, not private storage.

Local history is saved before the outbound Sheets request. Errors therefore leave the local analysis available. Bulk push processes all log rows, oldest first; `appendRow` provides no deduplication. Running it repeatedly can append duplicates.

### Known source contract gaps

| Area | Current code behavior |
|---|---|
| Broker column | Sender payload, TSV, and setup headers include broker; the embedded Apps Script row omits it. The script appends 20 values while the TSV has 21 columns. |
| Contract ranges | Local log retains contractLow/contractHigh; Sheets payload and TSV omit them. |
| Screenshot URL | Apps Script returns imageUrl and pushToSheets exposes it; analyze does not persist it onto the log entry. TSV exports only a Yes/blank screenshot-presence flag, not the Drive URL. |
| Setup help | The UI says the image is in Column M; the embedded script places it at the end of its 20-value row. |
| Success reporting | HTTP success is accepted, and invalid JSON falls back to status ok; a script-returned image error can still appear as a successful push. |

Treat these as observed limitations, not completed fixes. A future export repair should change the browser sender, receiver, setup headings, TSV, and saved-entry behavior together, while preserving existing data compatibility.

A Sheets connection test writes a row; screenshot exports may create files and links. Verify against disposable destinations. See [research](research.md) for the analysis side and [quickstart](../quickstart.md) for task routing.
