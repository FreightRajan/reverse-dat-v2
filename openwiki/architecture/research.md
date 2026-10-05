---
type: guide
title: Research and intelligence behavior
description: Understand screenshot extraction, shipper prompts, local corrections, and rate heuristics.
tags: [repository, development]
verified:
  - by: openwiki/0.7.0
    at: 2026-10-05T03:53:51.400Z
sources:
  - id: openwiki-source-f8d10828394c4129061d5b0e
    resource: repo://index.html
generated: { by: "codex", at: "2026-10-05T03:53:51.400Z" }
---

# Research and intelligence behavior

## Screenshot and research calls

Screenshot parsing and shipper research are separate Anthropic calls. `LoadForm.handleFile` reads an image as a data URL, submits its base64 content to `claude-sonnet-4-20250514`, strips optional JSON fences, parses the response, and merges extracted fields into the form. Missing values preserve previous form values. Equipment changes are accepted only when they match `EQUIP_TYPES`.

`App.analyze` builds a load and rate prompt, adds the saved intelligence context, and sends a Messages request with the `web_search_20250305` tool and a 2,000-token limit. The response is filtered to text blocks. The application asks for facility evidence and likely shippers; actual search execution and identification accuracy remain dependent on the API response.

`ResultCard` reads a terminal `[SHIPPER_LIST: ...]` tag for up to three prospect buttons and Google Maps searches, and hides that tag from the displayed prose. The log's `likelyShipper` still uses the first bold phrase via `extractShipper`, so the prospect list and logged value use different extraction contracts.

## Local intelligence is prompt context

Corrections, confirmations, and manual Intel entries are stored in `rdat_knowledge_base`. `loadKB` seeds it only when no stored value exists. `buildKBContext` calculates a `relevant` subset, but the actual prompt includes **all** stored entries. These entries are labeled verified and prioritized in the prompt. This is local user-maintained context; there is no model training call or independent verification of a confirmation.

Preserve that distinction in user-facing language. When changing matching or ranking, inspect both the prompt builder and `addCorrection`. The latter constructs a new array from the current `kb` closure, while Confirm All calls it repeatedly; exercise multi-confirm behavior before claiming every click is retained.

## Rate intelligence

These labels are deterministic heuristics from user-entered or parsed rate values, not a live market data feed:

| Label | Rule |
|---|---|
| VERY HARD | Posted < 85% of spot |
| HARD | Posted >= 85% and < 95% of spot |
| FAIR | Posted between 95% and 105% of spot, inclusive |
| EASY | Posted > 105% of spot |
| HOT | Spot > 110% of contract |
| WARM | Spot > contract and <= 110% of contract |
| COOL | Spot <= contract |

`rateIntel` also computes differences, percentages, and per-mile values. Truthiness guards suppress calculations for missing or zero amounts. Preserve dollar-total versus per-mile units across screenshot parsing, the form, and these calculations.

See [data and integrations](data-integrations.md) for history and export behavior, or [quickstart](../quickstart.md) for navigation.
