# 4. The Zoned Markdown Body

After the manifest, the body is rendered in Markdown with **zone markers** preserving the structural sequence and container boundaries of the source HTML.

!!! note "Translation is structural, not generic"
    Translation is a **structural DOM traversal**, not a generic HTML-to-Markdown conversion. Implementations walk the source DOM and emit a zone marker at each structural container boundary. Content elements are rendered as Markdown within the surrounding zone.

---

## Zone Markers

Zone markers use CommonMark link-label syntax and are **invisible when rendered**:

```
[//]: # (ames:zone zone-name)
[//]: # (ames:zone zone-name | permission-tuple)
```

### Zone Name Rules

A zone name must consist only of lowercase letters, digits, and hyphens, matching `[a-z0-9-]+`.

- The `data-ames` attribute value must already conform
- A derived tag name conforms by construction
- Where a `data-ames` value does not conform, consuming systems normalize it by lowercasing and replacing each run of disallowed characters with a single hyphen

Publishers carry the burden of supplying conformant `data-ames` values.

---

## Structural Parity

**Every structural container element** encountered during traversal produces a zone marker.

- The zone name is the `data-ames` attribute value if present; otherwise it is the element tag name
- Empty zones are valid — a structural container with no renderable Markdown content still emits a marker
- Structural parity is evaluated against the **static, server-delivered HTML source**, prior to the execution of any client-side scripts

### Zoned Markdown is Flat

Zone markers identify structural boundaries in traversal order; they do **not** open nested scopes. A new marker terminates the preceding zone. Named zones do not nest.

Nested semantic elements each generate their own marker in flat sequence:

```
<!-- Source: <main><article></article></main> -->

[//]: # (ames:zone main)
[//]: # (ames:zone article)
```

### Zone Sequence Example

```
<!-- Source HTML -->
<nav><main><article></article></main><footer>

<!-- Zoned Markdown output -->
[//]: # (ames:zone nav)
[//]: # (ames:zone main)
[//]: # (ames:zone article)
[//]: # (ames:zone footer)
```

Non-semantic markup with `data-ames` attributes produces the same sequence by attribute value:

```html
<div data-ames="primary-nav">...</div>
<div data-ames="footer-nav">...</div>
```

```
[//]: # (ames:zone primary-nav)
[//]: # (ames:zone footer-nav)
```

---

## Element Reference

### Structural Container Elements

These elements produce zone markers:

`<div>`, `<main>`, `<nav>`, `<header>`, `<footer>`, `<aside>`, `<article>`, `<section>`, `<form>`, `<search>`, `<figure>`, `<details>`, `<summary>`, `<dialog>`

### Content Elements

These render as Markdown within the surrounding zone and do **not** produce markers:

`<p>`, `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<li>`, `<blockquote>`, `<pre>`, `<table>`

### Excluded Elements

These do **not** produce zone markers and do **not** render as Markdown:

`<script>`, `<style>`, `<noscript>`, `<iframe>`, `<canvas>`, `<svg>`

!!! tip
    An SVG that carries substantive informational content may be declared in [Block 6 — Key Media](manifest/optional-blocks.md#36-block-6--key-media).

---

## Zone-Level Permission Overrides

A zone whose permissions differ from the page default carries its tuple in **two places**:

1. The zone marker in the `.ax.md` file
2. A `data-ames-policy` attribute on the corresponding source element

```html
<!-- Source HTML -->
<section data-ames="comments" data-ames-policy="1/0/0/0">
```

```
<!-- Zone marker in .ax.md -->
[//]: # (ames:zone comments | 1/0/0/0)
```

Zone permissions do **not** inherit from ancestor containers in the source DOM — each marker carries its own tuple if it differs from the page default.

!!! warning "Zone overrides require a page-level declaration"
    Zone-level overrides are invalid when no page-level `ames-policy` declaration is present. A malformed zone-level tuple is disregarded, and the zone is governed by the page-level declaration.

---

## Translation Conventions

### Absolute URLs

All hyperlinks and image URLs are resolved to absolute form.

- Declared media uses `ames-media:id` syntax
- Undeclared images render as standard Markdown image syntax with absolute URLs

### Tables

Tables are rendered as pipe-delimited Markdown when the source table contains a **discernible header row**.

!!! important
    Implementations must not manufacture header labels that are not present in the source. A table without a discernible source header row may be rendered using the closest faithful Markdown representation available, but any generated structure must not introduce labels, relationships, or meanings absent from the source.

---

## Ineligible Pages

A page whose content is **unbounded or non-deterministic** (such as an infinite-scroll feed) falls outside the structural parity model and is not a suitable candidate for AMES translation.

A page that relies on **client-side scripting** to manifest its content remains eligible — the publisher declares this with [`content.dynamic_dom: true`](manifest/content.md).
