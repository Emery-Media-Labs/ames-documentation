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

## 2. The `.ax` Document Format

The `.ax` document has two primary components:

| Component | Description |
|---|---|
| **AMES manifest** | Declares document identity, permissions, and content signals |
| **Translated body** | A structure-preserving translation of the canonical source content |

In the `.ax.md` serialization defined by this version:

- The **manifest** is a YAML block at the beginning of the file, using conventional YAML frontmatter delimiters (`---`)
- The **body** is rendered as zoned Markdown

### Manifest Blocks

The manifest contains six defined blocks:

| Block | Required | Purpose |
|---|---|---|
| Page Identity | ✅ | URL, publisher, title, locale, dates |
| Permissions | ✅ | Machine-readable usage permissions |
| Content | ✅ | Identifies the substantive content zone(s) |
| Topic Signals | Optional | Audience and subject matter signals |
| Visual Register | Optional | Logo, colors, typography |
| Key Media | Optional | Essential media assets |

### Permissions-Only Deployment

A publisher may use the AMES permissions infrastructure for pages that **do not provide a translated Markdown body**. This is a valid AMES use case when the publisher wishes to express machine usage permissions or discovery signals without publishing a full translation of the page.

---

## 3. Manifest

The AMES manifest is organized into **six defined blocks**. Blocks 1–3 are required; blocks 4–6 are optional.

!!! important "Normative constraints"
    The ordering of blocks and any character limits are normative. The blocks and fields defined in this section are the **complete set** of valid AMES manifest fields for the `.ax.md` document. Implementations must not introduce additional fields.

### Manifest Skeleton

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

---

### 3.1 Block 1 — Page Identity

**Required.** Identifies the document and its source page.

| Field | Type | Required | Description |
|---|---|---|---|
| `ames_version` | String | ✅ | Always `"0.9"` for this version |
| `canonical_url` | String | ✅ | Absolute URL of the human-facing source page |
| `publisher` | String | ✅ | Organization or publication responsible for this page |
| `title` | String | ✅ | Page title; must match the source page's HTML `<title>` |
| `locale` | String | Optional | IETF BCP 47 language tag (e.g. `"en-US"`). Maps from `<html lang="">` |
| `date_published` | String | Optional | ISO 8601 publication date |
| `date_modified` | String | Optional | ISO 8601 last-modified date |

**Example:**

```yaml
ames_version: "0.9"
canonical_url: "https://catchlightmag.com/articles/golden-hour-myth/"
publisher: "Catchlight"
title: "The Golden Hour Myth: Why the Best Light Is the Light You Understand"
locale: "en-US"
date_published: "2026-03-18T09:00:00Z"
date_modified: "2026-04-01T12:00:00Z"
```

---

### 3.2 Block 2 — Permissions

**Required.** The publisher's machine-readable statement of which uses of its content by automated systems are permitted or reserved.

A page participating in AMES carries a permissions block with **exactly one of two valid states**:

1. **AMES native permissions** — four required fields: `index`, `ephemeral`, `store`, and `train`
2. **`custom`** — the publisher's own terms govern; AMES makes no native permission grant

A permissions block that is present but blank is **invalid**.

#### 3.2.1 AMES Native Permissions

AMES expresses permissions through four independent positions:

```yaml
permissions:
  index: 1
  ephemeral: 1
  store: 0
  train: 0
```

- A value of `1` **permits** the specified use
- A value of `0` **reserves** the corresponding right
- All four positions must be declared; partial declarations are invalid
- Each position is independent — allowing one does not imply permission for any other

**Permission Positions:**

| Position | Field | Allow | Deny |
|---|---|---|---|
| **1** | `index` | Search indexing permitted. Permits transient reproduction of content, including the extraction or generation of snippets or equivalent navigational preview text or images, for the sole purpose of routing users to the source page, including temporary caching integral to that process. | Search indexing rights reserved. Transient reproduction and temporary caching of this content for search indexing purposes is not permitted. |
| **2** | `ephemeral` | Ephemeral retrieval permitted. Permits automated systems to retrieve and use content solely to fulfill an immediate, specific user request, provided the content is not retained beyond the lifecycle of that request. Within that single request, content may be synthesized or excerpted to serve as context for the user's immediate response, with meaningful citation of the source. Content may not be stored, indexed, reused, or retained beyond that request; this permission does not authorize the generation of reusable derived records or persistent substitute displays. This permission does not extend to systematic, scheduled, speculative, or preemptive retrieval; corpus-building; or text and data mining for model-development purposes. | Ephemeral retrieval rights reserved. Automated retrieval of this content to fulfill user requests, including agentic browsing, tool-mediated retrieval, or similar automated retrieval processes, is not permitted. |
| **3** | `store` | Persistent storage and synthesis permitted. Permits automated systems to ingest, persistently retain, and process content in persistent databases or stores across multiple, separate user requests, to produce summaries, synthesized outputs, or excerpts, with meaningful citation of the source. This permission governs retention and reuse of content beyond any single request; it is the persistence that distinguishes it from ephemeral retrieval. | Persistent storage and synthesis rights reserved. The ingestion, retention, or automated processing of this content to produce persistent summaries, synthesized outputs, or excerpts across multiple requests, beyond the sole purpose of routing users to the source page, is not permitted. |
| **4** | `train` | Model development permitted. Permits extraction and use of content for text and data mining for model-development purposes, including as training, fine-tuning, validation, benchmark, or evaluation data for model or system development, for machine-based systems that infer from input to generate outputs. This permission is not to be construed as a waiver of copyright and does not authorize reproduction, distribution, or publication of content beyond what the model-development process itself requires. | Model development rights reserved. Extraction or use of this content for text and data mining for model-development purposes, including as training, fine-tuning, validation, benchmark, or evaluation data for model or system development, is not permitted. |

!!! note "Distinguishing positions 2 and 3"
    Both `ephemeral` and `store` permit synthesis with meaningful citation. They are distinguished by **retention across requests**:

    - `ephemeral` = **synthesize-and-discard**: content is used within a single request and not retained beyond its lifecycle
    - `store` = **synthesize-and-retain**: content is ingested and held in persistent stores for reuse across multiple, separate requests

    The line is drawn by **persistence**, not by a system's internal model or the format of its output.

**Scope of Permissions:**

AMES permissions apply to the **content of the source page**, whether declared in the HTML head alone or also represented in an `.ax.md` translation file. They do **not** grant usage permissions for:

- Separately embodied media assets
- Source code, executable code, scripts, or stylesheets
- Embedded or third-party components or services
- Stock media or separately licensed materials

...unless those rights are independently held and granted outside AMES.

!!! warning "Legal disclaimer"
    AMES does not constitute legal advice, confer legal rights, or provide enforcement. Publishers remain responsible for understanding applicable law and enforcing their rights. Nothing in this specification waives or affects any copyright or other right held by a publisher.

#### 3.2.2 Custom Permissions

A publisher may point to its preferred permissions language or framework using the `custom` designation instead of AMES native fields.

```yaml
permissions: custom
```

With an optional URL pointer to the governing terms:

```yaml
permissions: custom https://example.com/terms
```

The value is read as a single string split on the first whitespace: the first token is the keyword `custom`, and any remainder is an optional absolute URL to the governing terms. Free text is not valid.

---

### 3.3 Block 3 — Content

**Required.** Identifies the zone or zones that constitute the substantive content of the source page.

#### `content.zone` (Required)

String or Array of Strings. Declares the zone or zones that constitute the substantive content.

=== "Single zone (String)"
    ```yaml
    content:
      zone: "article"
    ```

=== "Multiple zones (Array)"
    ```yaml
    content:
      zone: ["hero", "practice-areas"]
    ```
    Selected elements are interpreted in **source order**, regardless of array order.

!!! important
    The declared content zone(s) must correspond to the substantive content translated in the zoned Markdown body. The Content block must not introduce, summarize, replace, expand, or editorially alter the source content.

#### `content.dynamic_dom` (Optional)

Boolean. Defaults to `false`. Set to `true` when client-side scripts alter the structural container sequence of the declared content zone after the initial server response.

```yaml
content:
  zone: "main-content"
  dynamic_dom: true
```

---

### 3.4 Block 4 — Topic Signals (Optional)

Provides machine-readable signals about the page's subject matter and intended audience.

| Field | Type | Constraints | Description |
|---|---|---|---|
| `intended_audience` | String | Max 160 chars | The intended reader or use case |
| `core_topics` | Array of Strings | Max 8 items, max 40 chars each | Primary subject areas |

```yaml
topic_signals:
  intended_audience: "Intermediate to advanced photographers seeking to deepen craft rather than acquire gear."
  core_topics: ["photography", "natural light", "composition", "photographic technique", "visual literacy"]
```

---

### 3.5 Block 5 — Visual Register (Optional)

Describes the page's visual identity, enabling agents to represent the source accurately in visual contexts.

| Field | Type | Constraints | Description |
|---|---|---|---|
| `logo_url` | String | Absolute URL | Primary logo |
| `colors` | Array | Max 3 hex values | Primary, secondary, and accent colors in order of prominence |
| `typography` | String | Max 150 chars | Type choices, weight, and typographic character |
| `visual_direction` | String | Max 300 chars | Imagery style and overall visual character |

```yaml
visual_register:
  logo_url: "https://catchlightmag.com/assets/logo.svg"
  colors: ["#1a1a1a", "#f5f2ee", "#c8773a"]
  typography: "Clean serif headlines with generous leading; neutral sans-serif body."
  visual_direction: "Documentary and fine art photography. High contrast, minimal post-processing."
```

---

### 3.6 Block 6 — Key Media (Optional)

Identifies media **essential to a machine reader's understanding** of the page. Decorative imagery is omitted. Declared under the top-level `media` key as a list of items.

| Field | Required | Description |
|---|---|---|
| `id` | ✅ | Unique identifier within this file. Referenced in the body via `ames-media:id` |
| `url` | ✅ | Absolute, publicly resolvable URL |
| `type` | ✅ | One of: `diagram`, `photo`, `chart`, `illustration`, `screenshot`, `video`, `audio`, `embed` |
| `alt_text` | ✅ | Human-facing accessibility description |
| `transcript_url` | Optional | Absolute URL to an existing transcript (for `video`/`audio`) |
| `ocr_text` | Optional | Extractable text visible within the image (image types only) |

Declared media is referenced in the body using `ames-media:id` syntax:

```markdown
![Descriptive alt text](ames-media:exposure-diagram)
```

!!! warning
    A declared media item must **not** also be embedded or referenced by its ordinary source URL as standard Markdown image syntax or as a raw URL in the body.

---

## 4. The Zoned Markdown Body

After the manifest, the body is rendered in Markdown with **zone markers** preserving the structural sequence and container boundaries of the source HTML.

!!! note "Translation is structural, not generic"
    Translation is a **structural DOM traversal**, not a generic HTML-to-Markdown conversion. Implementations walk the source DOM and emit a zone marker at each structural container boundary. Content elements are rendered as Markdown within the surrounding zone.

### Zone Markers

Zone markers use CommonMark link-label syntax and are **invisible when rendered**:

```
[//]: # (ames:zone zone-name)
[//]: # (ames:zone zone-name | permission-tuple)
```

A zone name must consist only of lowercase letters, digits, and hyphens, matching `[a-z0-9-]+`.

### Structural Parity

**Every structural container element** encountered during traversal produces a zone marker. The zone name is the `data-ames` attribute value if present; otherwise it is the element tag name. Empty zones are valid.

Structural parity is evaluated against the **static, server-delivered HTML source**, prior to the execution of any client-side scripts.

**Zoned Markdown is flat** — zone markers identify structural boundaries in traversal order and do not open nested scopes. A new marker terminates the preceding zone.

**Zone sequence example:**

```
<!-- Source HTML: <nav><main><article></article></main><footer> -->

[//]: # (ames:zone nav)
[//]: # (ames:zone main)
[//]: # (ames:zone article)
[//]: # (ames:zone footer)
```

### Element Reference

**Structural container elements** (produce zone markers):
`<div>`, `<main>`, `<nav>`, `<header>`, `<footer>`, `<aside>`, `<article>`, `<section>`, `<form>`, `<search>`, `<figure>`, `<details>`, `<summary>`, `<dialog>`

**Content elements** (render as Markdown, no markers):
`<p>`, `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<li>`, `<blockquote>`, `<pre>`, `<table>`

**Excluded elements** (no markers, no Markdown):
`<script>`, `<style>`, `<noscript>`, `<iframe>`, `<canvas>`, `<svg>`

### Zone-Level Permission Overrides

A zone whose permissions differ from the page default carries its tuple in **two places**:

```html
<!-- Source HTML -->
<section data-ames="comments" data-ames-policy="1/0/0/0">
```

```
<!-- Zone marker in .ax.md -->
[//]: # (ames:zone comments | 1/0/0/0)
```

!!! warning "Zone overrides require a page-level declaration"
    Zone-level overrides are invalid when no page-level `ames-policy` declaration is present. A malformed zone-level tuple is disregarded, and the zone is governed by the page-level declaration.

### Translation Conventions

- **Absolute URLs:** All hyperlinks and image URLs are resolved to absolute form.
- **Tables:** Rendered as pipe-delimited Markdown when the source table contains a discernible header row. Implementations must not manufacture header labels not present in the source.

### Ineligible Pages

A page whose content is **unbounded or non-deterministic** (such as an infinite-scroll feed) falls outside the structural parity model and is not a suitable candidate for AMES translation. Pages relying on client-side scripting remain eligible — the publisher declares this with `content.dynamic_dom: true`.

---

## 5. Site-Level Declaration & Permissions Cascade

### 5.1 Site-Level Declaration

An AMES deployment **should** include an `ames-ai.txt` file at `/.well-known/ames-ai.txt`. The file is recommended, not required. Consuming systems discover it by convention at that fixed path — it is not linked from individual pages.

The file uses flat `Key: Value` lines. Lines beginning with `#` are comments.

| Field | Required | Description |
|---|---|---|
| `AMES-Version` | ✅ | Always `0.9` for this version |
| `AMES-Publisher` | ✅ | The organization responsible for the domain |
| `AMES-Domain` | ✅ | The domain governed by the declaration |
| `AMES-Policy` | ✅ | Site-wide permission state: four-position tuple or `custom` |
| `AMES-Positions` | ✅ (tuple state) | Tuple legend; in this version, exactly `index/ephemeral/store/train` |
| `AMES-Values` | ✅ (tuple state) | Value legend; `1=allow, 0=deny` |
| `AMES-Locale` | Optional | IETF BCP 47 primary language tag for the domain |
| `AMES-Spec` | ✅ | Absolute URL identifying the AMES specification version |

!!! important
    The fields above are the **complete set** of valid fields for the site-level declaration file. Implementations must not introduce additional fields.

**Example — Tuple State:**

```text
AMES-Version: 0.9
AMES-Policy: 1/1/0/0
AMES-Positions: index/ephemeral/store/train
AMES-Values: 1=allow, 0=deny
AMES-Publisher: Catchlight
AMES-Domain: catchlightmag.com
AMES-Spec: https://openames.org/v0.9
```

**Example — Custom State:**

```text
AMES-Version: 0.9
AMES-Policy: custom https://catchlightmag.com/terms/ai-usage
AMES-Publisher: Catchlight
AMES-Domain: catchlightmag.com
AMES-Spec: https://openames.org/v0.9
```

### 5.2 Permissions Cascade

AMES permissions resolve from the **most specific valid declaration** to the **broadest applicable**:

```
Zone  →  Page  →  Site
```

| Scenario | Resolution |
|---|---|
| Valid zone-level declaration present | Zone declaration governs |
| No zone-level declaration | Page-level declaration governs |
| Malformed page-level declaration | Falls back to site-level |
| No site-level declaration | No AMES declaration exists for the content |
| Zone-level override, no page-level declaration | Zone override is **invalid** |
| Malformed zone-level tuple | Disregarded; zone governed by page-level declaration |

---

## 6. Source Header & Discovery

### 6.1 The HTML Source Header

A source page carries its page-level AMES declaration in the HTML `<head>` using three elements.

**1. Usage Reference (required):**

```html
<link rel="automated-usage" href="https://openames.org/v0.9" />
```

**2. Permissions Declaration (required):**

=== "Tuple State"
    ```html
    <meta name="ames-policy"
          content="1/1/0/0"
          data-positions="index/ephemeral/store/train" />
    ```

=== "Custom State"
    ```html
    <meta name="ames-policy" content="custom"
          data-notice="https://example.com/terms" />
    ```

**3. Exchange Document Link (required when a translation exists):**

```html
<link rel="alternate" type="text/markdown"
      href="https://example.com/page.ax.md" />
```

!!! warning "Consistency requirement"
    A `permissions` block in an `.ax.md` file and the `ames-policy` declaration in the source `<head>` express the same page-level permission state and **must agree**.

Zone-level overrides travel on the source element as `data-ames-policy`:

```html
<section data-ames="comments" data-ames-policy="1/0/0/0">
```

### 6.2 Other Discovery Mechanisms

**Sitemap injection (recommended):** `.ax.md` URLs are listed alongside their HTML counterparts in `sitemap.xml`, each with its own `<lastmod>`.

**LLM hinting (informational):** Publishers may list AMES URLs in `llms.txt` with explicit Markdown links.

---

## 7. Reserved Extensions

The following extensions are reserved in AMES 0.9. They are **named but not specified**, and are dormant in this version.

| Extension | Reserved For |
|---|---|
| **`.ax.json`** | JSON serialization of the agentic exchange document. The `.ax.json` suffix and `application/ames+json` MIME type are reserved. |
| **`content_integrity`** (manifest block) | Reserved for cryptographic content verification, including fingerprinting, normalization, and verification status. |

---

## 8. Worked Example

A complete HTML page and its `.ax.md` translation using a fictional photography magazine, **Catchlight**.

**Permission configuration:** permits indexing and ephemeral retrieval; reserves store and train (`1/1/0/0`). The comments zone, carrying content the publisher does not own, reserves ephemeral as well (`1/0/0/0`).

### Source HTML Page

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
  <meta charset="utf-8" />
  <title>The Golden Hour Myth: Why the Best Light Is the Light You Understand</title>

  <link rel="automated-usage" href="https://openames.org/v0.9" />
  <meta name="ames-policy" content="1/1/0/0" data-positions="index/ephemeral/store/train" />
  <link rel="alternate" type="text/markdown"
        href="https://catchlightmag.com/articles/golden-hour-myth.ax.md" />
  <link rel="canonical" href="https://catchlightmag.com/articles/golden-hour-myth/" />
</head>
<body>
  <header data-ames="header">
    <p>Catchlight | For photographers who think</p>
  </header>

  <nav data-ames="primary-nav">
    <ul>
      <li><a href="/features">Features</a></li>
      <li><a href="/technique">Technique</a></li>
    </ul>
  </nav>

  <article data-ames="article">
    <h1>The Golden Hour Myth: Why the Best Light Is the Light You Understand</h1>
    <p><strong>By Maya Osei</strong> | Published March 18, 2026 | Technique</p>
    <p>Every photography tutorial says the same thing: shoot at golden hour. But chasing
    golden hour is a crutch — the photographers who transcend it are the ones who learn
    to see in any light.</p>
    <img src="/assets/articles/golden-hour-myth/midday-comparison.jpg"
         alt="Side-by-side portraits: golden hour at left, managed midday shade at right." />
  </article>

  <aside data-ames="related">
    <h2>Related Articles</h2>
    <ul>
      <li><a href="/technique/reading-shadows">Reading Shadows: A Field Guide</a></li>
    </ul>
  </aside>

  <section data-ames="comments" data-ames-policy="1/0/0/0">
    <h2>Comments</h2>
  </section>

  <footer data-ames="footer">
    <p>&copy; 2026 Catchlight Magazine</p>
  </footer>
</body>
</html>
```

### Translation File: `golden-hour-myth.ax.md`

```markdown
---
ames_version: "0.9"
canonical_url: "https://catchlightmag.com/articles/golden-hour-myth/"
publisher: "Catchlight"
title: "The Golden Hour Myth: Why the Best Light Is the Light You Understand"
locale: "en-US"
date_published: "2026-03-18T09:00:00Z"

permissions:
  index: 1
  ephemeral: 1
  store: 0
  train: 0

content:
  zone: "article"

topic_signals:
  intended_audience: "Intermediate to advanced photographers seeking to deepen craft rather than acquire gear."
  core_topics: ["photography", "natural light", "composition", "photographic technique", "visual literacy"]

visual_register:
  logo_url: "https://catchlightmag.com/assets/logo.svg"
  colors: ["#1a1a1a", "#f5f2ee", "#c8773a"]
  typography: "Clean serif headlines with generous leading; neutral sans-serif body."
  visual_direction: "Documentary and fine art photography. High contrast, minimal post-processing."

media:
  - id: "midday-portrait-comparison"
    url: "https://catchlightmag.com/assets/articles/golden-hour-myth/midday-comparison.jpg"
    type: "photo"
    alt_text: "Side-by-side portraits: golden hour at left, managed midday shade at right."
---

[//]: # (ames:zone header)
Catchlight | For photographers who think

[//]: # (ames:zone primary-nav)
- [Features](https://catchlightmag.com/features)
- [Technique](https://catchlightmag.com/technique)

[//]: # (ames:zone article)
# The Golden Hour Myth: Why the Best Light Is the Light You Understand

**By Maya Osei** | Published March 18, 2026 | Technique

Every photography tutorial says the same thing: shoot at golden hour. But chasing golden
hour is a crutch — the photographers who transcend it are the ones who learn to see in
any light.

![Side-by-side portraits: golden hour vs. managed midday](ames-media:midday-portrait-comparison)

[//]: # (ames:zone related)
## Related Articles
- [Reading Shadows: A Field Guide](https://catchlightmag.com/technique/reading-shadows)

[//]: # (ames:zone comments | 1/0/0/0)
## Comments

[//]: # (ames:zone footer)
© 2026 Catchlight Magazine
```

### Annotations

**Why does the `comments` zone have a different policy?**
The publisher does not own the user-generated content in the comments zone and therefore cannot grant ephemeral retrieval rights for it. The zone-level override `1/0/0/0` permits indexing but reserves ephemeral retrieval.

**Why is `content.zone` only `"article"` and not all zones?**
The `content.zone` field identifies **substantive content** — the body of the article itself. Navigation, sidebars, comments, and footers are structural chrome that agents should not treat as primary content.

**What does the `ames-media:` reference do?**
It points to the media item declared in Block 6 — Key Media, ensuring the image is properly attributed and typed rather than treated as a generic inline image.
