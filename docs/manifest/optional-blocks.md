# 3.4–3.6 Optional Blocks

## 3.4 Block 4 — Topic Signals

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

## 3.5 Block 5 — Visual Register

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

## 3.6 Block 6 — Key Media

Identifies media **essential to a machine reader's understanding** of the page. Decorative imagery is omitted. Declared under the top-level `media` key as a list of items.

### Fields

| Field | Required | Description |
|---|---|---|
| `id` | ✅ | Unique identifier within this file. Referenced in the body via `ames-media:id` |
| `url` | ✅ | Absolute, publicly resolvable URL |
| `type` | ✅ | One of: `diagram`, `photo`, `chart`, `illustration`, `screenshot`, `video`, `audio`, `embed` |
| `alt_text` | ✅ | Human-facing accessibility description |
| `transcript_url` | Optional | Absolute URL to an existing transcript (for `video`/`audio`) |
| `ocr_text` | Optional | Extractable text visible within the image (image types only) |

### Example

```yaml
media:
  - id: "midday-portrait-comparison"
    url: "https://catchlightmag.com/assets/articles/golden-hour-myth/midday-comparison.jpg"
    type: "photo"
    alt_text: "Side-by-side portraits: golden hour at left, managed midday shade at right, producing equally compelling contrast."
```

### Referencing Media in the Body

Declared media is referenced in the zoned Markdown body using `ames-media:id` syntax:

```markdown
![Descriptive alt text](ames-media:midday-portrait-comparison)
```

!!! warning
    A declared media item must **not** also be embedded or referenced by its ordinary source URL as standard Markdown image syntax or as a raw URL in the body.
