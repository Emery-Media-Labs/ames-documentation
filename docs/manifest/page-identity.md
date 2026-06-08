# 3.1 Block 1 — Page Identity

**Required.** Identifies the document and its source page.

## Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `ames_version` | String | ✅ | Always `"0.9"` for this version |
| `canonical_url` | String | ✅ | Absolute URL of the human-facing source page |
| `publisher` | String | ✅ | Organization or publication responsible for this page |
| `title` | String | ✅ | Page title; must match the source page's HTML `<title>` |
| `locale` | String | Optional | IETF BCP 47 language tag (e.g. `"en-US"`). Maps from `<html lang="">` |
| `date_published` | String | Optional | ISO 8601 publication date |
| `date_modified` | String | Optional | ISO 8601 last-modified date |

## Example

```yaml
ames_version: "0.9"
canonical_url: "https://catchlightmag.com/articles/golden-hour-myth/"
publisher: "Catchlight"
title: "The Golden Hour Myth: Why the Best Light Is the Light You Understand"
locale: "en-US"
date_published: "2026-03-18T09:00:00Z"
date_modified: "2026-04-01T12:00:00Z"
```
