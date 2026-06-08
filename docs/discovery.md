# 6. Source Header & Discovery

## 6.1 The HTML Source Header

A source page carries its page-level AMES declaration in the HTML `<head>` using three required elements.

### Required Tags

#### 1. Usage Reference

Links to the AMES specification version governing this page. The `rel` value is generic and not AMES-specific; the `href` value ties the page to AMES.

```html
<link rel="automated-usage" href="https://openames.org/v0.9" />
```

#### 2. Permissions Declaration

Declares the page-level permission state.

=== "Tuple State"
    ```html
    <meta name="ames-policy"
          content="1/1/0/0"
          data-positions="index/ephemeral/store/train" />
    ```
    `content` is the four-position permission tuple; `data-positions` is present and, in this version, exactly `index/ephemeral/store/train`.

=== "Custom State"
    ```html
    <!-- Without URL pointer -->
    <meta name="ames-policy" content="custom" />

    <!-- With optional URL pointer -->
    <meta name="ames-policy"
          content="custom"
          data-notice="https://example.com/terms" />
    ```
    `data-positions` is omitted (no tuple to legend). `data-notice` is optional and carries an absolute URL to the publisher's terms. `data-notice` is used **only** in the custom state.

#### 3. Exchange Document Link

Required when a translation file exists. Links the source page to its `.ax.md` translation.

```html
<link rel="alternate" type="text/markdown"
      href="https://example.com/page.ax.md" />
```

### Full Example

```html
<head>
  <link rel="automated-usage" href="https://openames.org/v0.9" />

  <meta name="ames-policy"
        content="1/1/0/0"
        data-positions="index/ephemeral/store/train" />

  <link rel="alternate" type="text/markdown"
        href="https://catchlightmag.com/articles/golden-hour-myth.ax.md" />
</head>
```

### Consistency Requirement

!!! warning
    A `permissions` block in an `.ax.md` file and the `ames-policy` declaration in the source `<head>` express the same page-level permission state and **must agree**.

### Zone-Level Overrides in HTML

Zone-level permission overrides travel on the source element as `data-ames-policy`:

```html
<section data-ames="comments" data-ames-policy="1/0/0/0">
```

---

## 6.2 Other Discovery Mechanisms

### Sitemap Injection (Recommended)

`.ax.md` URLs are listed alongside their HTML counterparts in `sitemap.xml`, each with its own `<lastmod>`:

```xml
<urlset>
  <url>
    <loc>https://example.com/page/</loc>
    <lastmod>2026-06-01</lastmod>
  </url>
  <url>
    <loc>https://example.com/page.ax.md</loc>
    <lastmod>2026-06-01</lastmod>
  </url>
</urlset>
```

### LLM Hinting (Informational)

Publishers may list AMES URLs in `llms.txt` with explicit Markdown links. This is informational and not required for AMES compliance.

### Site-Level Declaration Discovery

The `/.well-known/ames-ai.txt` file is found by convention at that fixed path — it is **not** linked from individual pages. See [Section 5 — Site-Level Declaration](site-declaration.md).
