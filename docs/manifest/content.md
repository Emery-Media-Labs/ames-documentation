# 3.3 Block 3 — Content

**Required.** Identifies the zone or zones that constitute the substantive content of the source page. This block distinguishes the primary body of the work from navigation, chrome, advertising, comments, recommendation modules, related-content regions, and other non-substantive page elements.

## Fields

### `content.zone` (Required)

String or Array of Strings. Declares the zone or zones that constitute the substantive content.

=== "Single zone (String)"
    ```yaml
    content:
      zone: "article"
    ```
    All elements matching this zone name are selected and treated as the substantive content zone.

=== "Multiple zones (Array)"
    ```yaml
    content:
      zone: ["hero", "practice-areas"]
    ```
    All elements matching each declared zone name are selected. The order of zone names in the array does **not** control content order — selected elements are interpreted in **source order**.

!!! important
    The declared content zone(s) must correspond to the substantive content translated in the zoned Markdown body. The Content block identifies the source content region; it must not introduce, summarize, replace, expand, or editorially alter the source content.

### `content.dynamic_dom` (Optional)

Boolean. Defaults to `false`.

Set to `true` when client-side scripts alter the structural container sequence of the declared content zone after the initial server response, so that the translated body is a **representative capture** of the zone rather than a frame-exact match.

```yaml
content:
  zone: "main-content"
  dynamic_dom: true
```
