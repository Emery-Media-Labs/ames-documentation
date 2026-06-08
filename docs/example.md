# 8. Worked Example

A complete HTML page and its `.ax.md` translation using a fictional photography magazine, **Catchlight**.

**Permission configuration:** permits indexing and ephemeral retrieval; reserves store and train (`1/1/0/0`). The comments zone, carrying content the publisher does not own, reserves ephemeral as well (`1/0/0/0`).

---

## Source HTML Page

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>The Golden Hour Myth: Why the Best Light Is the Light You Understand</title>

  <link rel="automated-usage" href="https://openames.org/v0.9" />
  <meta name="ames-policy" content="1/1/0/0" data-positions="index/ephemeral/store/train" />
  <link rel="alternate" type="text/markdown"
        href="https://catchlightmag.com/articles/golden-hour-myth.ax.md" />

  <link rel="canonical" href="https://catchlightmag.com/articles/golden-hour-myth/" />
  <meta property="og:title" content="The Golden Hour Myth" />

  <link rel="stylesheet" href="/assets/css/typography.css" />
  <link rel="stylesheet" href="/assets/css/article.css" />
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
         alt="Side-by-side portraits: golden hour at left, managed midday shade at right,
         producing equally compelling contrast." />
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

---

## Translation File: `golden-hour-myth.ax.md`

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
    alt_text: "Side-by-side portraits: golden hour at left, managed midday shade at right,
    producing equally compelling contrast."
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

---

## Annotations

### Why does the `comments` zone have a different policy?

The comments zone uses `data-ames-policy="1/0/0/0"` — it permits indexing (`1`) but reserves ephemeral retrieval (`0`). This reflects that the publisher does not own the user-generated content in that zone and therefore cannot grant ephemeral retrieval rights for it.

This is an example of the [zone-level permission override](zoned-body.md#zone-level-permission-overrides) mechanism.

### Why is the `content.zone` only `"article"` and not all zones?

The `content.zone` field identifies **substantive content** — the body of the article itself. Navigation, sidebars, comments, and footers are structural chrome. An agent understanding the page's primary content should focus on the `article` zone.

### What does the `ames-media:` reference do?

The `ames-media:midday-portrait-comparison` reference in the body points to the media item declared in Block 6 — Key Media. This ensures the image is properly attributed and typed rather than being treated as a generic inline image. See [Block 6 — Key Media](manifest/optional-blocks.md#36-block-6--key-media).
