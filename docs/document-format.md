# 2. The `.ax` Document Format

The `.ax` document has two primary components:

| Component | Description |
|---|---|
| **AMES manifest** | Declares document identity, permissions, and content signals |
| **Translated body** | A structure-preserving translation of the canonical source content |

In the `.ax.md` serialization defined by this version:

- The **manifest** is a YAML block at the beginning of the file, using conventional YAML frontmatter delimiters (`---`)
- The **body** is rendered as zoned Markdown

## Manifest Blocks

The manifest contains six defined blocks:

| Block | Required | Purpose |
|---|---|---|
| Page Identity | ✅ | URL, publisher, title, locale, dates |
| Permissions | ✅ | Machine-readable usage permissions |
| Content | ✅ | Identifies the substantive content zone(s) |
| Topic Signals | Optional | Audience and subject matter signals |
| Visual Register | Optional | Logo, colors, typography |
| Key Media | Optional | Essential media assets |

Each block is defined in detail in [Section 3 — Manifest](manifest/index.md).

## Permissions-Only Deployment

A publisher may use the AMES permissions infrastructure for pages that **do not provide a translated Markdown body**. This is a valid AMES use case when the publisher wishes to express machine usage permissions or discovery signals without publishing a full translation of the page.
