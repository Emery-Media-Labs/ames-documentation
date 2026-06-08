# 3.2 Block 2 — Permissions

**Required.** The publisher's machine-readable statement of which uses of its content by automated systems are permitted or reserved.

A page participating in AMES carries a permissions block with **exactly one of two valid states**:

1. **AMES native permissions** — four required fields: `index`, `ephemeral`, `store`, and `train`
2. **`custom`** — the publisher's own terms govern; AMES makes no native permission grant

A permissions block that is present but blank is **invalid**.

---

## 3.2.1 AMES Native Permissions

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

### Permission Positions

| Position | Field | Allow | Deny |
|---|---|---|---|
| **1** | `index` | Search indexing permitted. Permits transient reproduction of content, including the extraction or generation of snippets or equivalent navigational preview text or images, for the sole purpose of routing users to the source page, including temporary caching integral to that process. | Search indexing rights reserved. Transient reproduction and temporary caching of this content for search indexing purposes is not permitted. |
| **2** | `ephemeral` | Ephemeral retrieval permitted. Permits automated systems to retrieve and use content solely to fulfill an immediate, specific user request, provided the content is not retained beyond the lifecycle of that request. Within that single request, content may be synthesized or excerpted to serve as context for the user's immediate response, with meaningful citation of the source. Content may not be stored, indexed, reused, or retained beyond that request; this permission does not authorize the generation of reusable derived records or persistent substitute displays. This permission does not extend to systematic, scheduled, speculative, or preemptive retrieval; corpus-building; or text and data mining for model-development purposes. | Ephemeral retrieval rights reserved. Automated retrieval of this content to fulfill user requests, including agentic browsing, tool-mediated retrieval, or similar automated retrieval processes, is not permitted. |
| **3** | `store` | Persistent storage and synthesis permitted. Permits automated systems to ingest, persistently retain, and process content in persistent databases or stores across multiple, separate user requests, to produce summaries, synthesized outputs, or excerpts, with meaningful citation of the source. This permission governs retention and reuse of content beyond any single request; it is the persistence that distinguishes it from ephemeral retrieval. | Persistent storage and synthesis rights reserved. The ingestion, retention, or automated processing of this content to produce persistent summaries, synthesized outputs, or excerpts across multiple requests, beyond the sole purpose of routing users to the source page, is not permitted. |
| **4** | `train` | Model development permitted. Permits extraction and use of content for text and data mining for model-development purposes, including as training, fine-tuning, validation, benchmark, or evaluation data for model or system development, for machine-based systems that infer from input to generate outputs. This permission is not to be construed as a waiver of copyright and does not authorize reproduction, distribution, or publication of content beyond what the model-development process itself requires. | Model development rights reserved. Extraction or use of this content for text and data mining for model-development purposes, including as training, fine-tuning, validation, benchmark, or evaluation data for model or system development, is not permitted. |

### Ephemeral vs. Store

!!! note "Distinguishing positions 2 and 3"
    Both `ephemeral` and `store` permit synthesis with meaningful citation. They are distinguished by **retention across requests**:

    - `ephemeral` = **synthesize-and-discard**: content is used within a single request and not retained beyond its lifecycle
    - `store` = **synthesize-and-retain**: content is ingested and held in persistent stores for reuse across multiple, separate requests

    The line is drawn by **persistence**, not by a system's internal model or the format of its output.

### Scope of Permissions

AMES permissions apply to the **content of the source page**, whether declared in the HTML head alone or also represented in an `.ax.md` translation file. They do **not** grant usage permissions for:

- Separately embodied media assets
- Source code, executable code, scripts, or stylesheets
- Embedded or third-party components or services
- Stock media or separately licensed materials

...unless those rights are independently held and granted outside AMES.

!!! warning "Legal disclaimer"
    AMES does not constitute legal advice, confer legal rights, or provide enforcement. Publishers remain responsible for understanding applicable law and enforcing their rights. Nothing in this specification waives or affects any copyright or other right held by a publisher.

---

## 3.2.2 Custom Permissions

A publisher may point to its preferred permissions language or framework using the `custom` designation instead of AMES native fields.

```yaml
permissions: custom
```

With an optional URL pointer to the governing terms:

```yaml
permissions: custom https://example.com/terms
```

The value is read as a single string split on the first whitespace:

- First token: the keyword `custom`
- Remainder (optional): an absolute URL to the governing terms

Free text (other than an absolute URL) is not valid. AMES does not retrieve, interpret, validate, or standardize the resource the pointer identifies.

### When to use `custom`

Use `custom` when your site is already governed by explicit AI/automated usage terms and you want AMES to point to them rather than restate them in the four-position tuple format.
