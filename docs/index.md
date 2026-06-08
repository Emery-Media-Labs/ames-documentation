# AMES — Agent and Machine Exchange Standard

**Version 0.9 RFC** · June 5, 2026

> **Status:** v0.9 RFC. Stable enough for implementation and feedback. Breaking changes before 1.0 will be clearly marked.

---

## What is AMES?

The Agent and Machine Exchange Standard defines a format for **agentic exchange documents** — permission-aware companion representations of canonical web content, expressed as Agent Exchange (`.ax`) files.

This version defines the Markdown serialization: **`.ax.md`**.

AMES makes web content more legible to automated systems without reducing it to undifferentiated extracted text. Conventional extraction methods often:

- Flatten structural hierarchy
- Omit visual and contextual relationships
- Separate content from attribution
- Discard rights or usage signals

AMES provides a standardized translation format that preserves content structure, publisher intent, attribution context, and **machine-readable permissions** for automated use.

---

## What AMES Is — and Isn't

AMES is a **translation and signaling format**. It expresses content structure, semantic context, attribution information, and publisher-defined usage permissions.

AMES does **not**:

- Prescribe crawler architecture
- Require any specific retrieval system
- Dictate agent behavior
- Arbitrate content quality
- Determine the legal effect of any permission statement

### Relationship to `robots.txt`

AMES does not replace or override `robots.txt`. The two mechanisms operate at different layers and are designed to coexist:

| Mechanism | Controls |
|---|---|
| `robots.txt` | Crawl **access** — whether a bot may fetch a page |
| AMES permissions | **Use** — what an automated system may do with content once it has access |

---

## Quick Start

A minimal AMES deployment has three parts:

=== "1. HTML Head Tags"
    ```html
    <link rel="automated-usage" href="https://openames.org/v0.9" />

    <meta name="ames-policy"
          content="1/1/0/0"
          data-positions="index/ephemeral/store/train" />

    <link rel="alternate" type="text/markdown"
          href="https://example.com/page.ax.md" />
    ```

=== "2. Translation File (`page.ax.md`)"
    ```yaml
    ---
    ames_version: "0.9"
    canonical_url: "https://example.com/page/"
    publisher: "Example Publisher"
    title: "Page Title"

    permissions:
      index: 1
      ephemeral: 1
      store: 0
      train: 0

    content:
      zone: "article"
    ---

    [//]: # (ames:zone article)
    # Page Title
    Page content here...
    ```

=== "3. Site Declaration (`ames-ai.txt`)"
    ```text
    AMES-Version: 0.9
    AMES-Policy: 1/1/0/0
    AMES-Positions: index/ephemeral/store/train
    AMES-Values: 1=allow, 0=deny
    AMES-Publisher: Example Publisher
    AMES-Domain: example.com
    AMES-Spec: https://openames.org/v0.9
    ```

---

## Specification Sections

| Section | Description |
|---|---|
| [2. Document Format](document-format.md) | The `.ax` document model: manifest + zoned Markdown body |
| [3. Manifest](manifest/index.md) | All six manifest blocks: identity, permissions, content, topic signals, visual register, and key media |
| [4. Zoned Markdown Body](zoned-body.md) | Structural DOM traversal, zone markers, and translation conventions |
| [5. Site-Level Declaration](site-declaration.md) | The `ames-ai.txt` file and the permissions cascade (zone → page → site) |
| [6. Discovery](discovery.md) | HTML head tags, sitemap injection, and LLM hinting |
| [7. Reserved Extensions](extensions.md) | `.ax.json` and `content_integrity` reserved for future versions |
| [8. Worked Example](example.md) | A complete HTML page and its `.ax.md` translation, annotated |
