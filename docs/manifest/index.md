# 3. Manifest

The AMES manifest is organized into **six defined blocks**. Blocks 1–3 are required; blocks 4–6 are optional.

!!! important "Normative constraints"
    The ordering of blocks and any character limits are normative. The blocks and fields defined in this section are the **complete set** of valid AMES manifest fields for the `.ax.md` document. Implementations must not introduce additional fields.

## Block Overview

| # | Block | Required | Section |
|---|---|---|---|
| 1 | [Page Identity](page-identity.md) | ✅ | 3.1 |
| 2 | [Permissions](permissions.md) | ✅ | 3.2 |
| 3 | [Content](content.md) | ✅ | 3.3 |
| 4 | [Topic Signals](optional-blocks.md#34-block-4--topic-signals) | Optional | 3.4 |
| 5 | [Visual Register](optional-blocks.md#35-block-5--visual-register) | Optional | 3.5 |
| 6 | [Key Media](optional-blocks.md#36-block-6--key-media) | Optional | 3.6 |

## Manifest Skeleton

```yaml
---
# Block 1 — Page Identity (Required)
ames_version: "0.9"
canonical_url: "https://example.com/page/"
publisher: "Publisher Name"
title: "Page Title"
locale: "en-US"
date_published: "2026-01-01T00:00:00Z"
date_modified: "2026-06-01T00:00:00Z"

# Block 2 — Permissions (Required)
permissions:
  index: 1
  ephemeral: 1
  store: 0
  train: 0

# Block 3 — Content (Required)
content:
  zone: "article"

# Block 4 — Topic Signals (Optional)
topic_signals:
  intended_audience: "Description of intended reader."
  core_topics: ["topic1", "topic2"]

# Block 5 — Visual Register (Optional)
visual_register:
  logo_url: "https://example.com/logo.svg"
  colors: ["#000000", "#ffffff", "#0066cc"]
  typography: "Brief description of type choices."
  visual_direction: "Brief description of imagery style."

# Block 6 — Key Media (Optional)
media:
  - id: "unique-id"
    url: "https://example.com/image.jpg"
    type: "photo"
    alt_text: "Accessible description."
---
```
