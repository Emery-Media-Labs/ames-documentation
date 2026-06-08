# 5. Site-Level Declaration & Permissions Cascade

## 5.1 Site-Level Declaration

An AMES deployment **should** include an `ames-ai.txt` file at `/.well-known/ames-ai.txt`. The file is recommended, not required, and is not required to be linked from individual pages — consuming systems discover it by convention at that fixed path.

Because the site-level declaration is discovered independently of any page-level context, it is **self-identifying**.

### Format

The file uses flat `Key: Value` lines.

- Lines beginning with `#` are comments
- Comments carry human-readable notice and do not modify the keyed fields
- Field keys use the `AMES-` prefix

This format avoids MIME-type and server-configuration friction on shared hosting and requires no YAML or Markdown parser.

### Fields

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

### Example — Tuple State

```text
# Notice
# This file provides a site-level AMES declaration for automated systems.
# Page-level AMES declarations control where present. Where no valid
# page-level AMES declaration is present, the AMES-Policy in this file
# states the publisher's site-level default.
#
# The meaning of the AMES-Policy is defined by the AMES specification
# identified by AMES-Spec. Except as expressly permitted by a valid AMES
# declaration, all copyright, related rights, neighboring rights, database
# rights, text-and-data-mining rights, and other applicable rights are
# reserved, including for purposes of Article 4 of Directive (EU) 2019/790
# where applicable.

AMES-Version: 0.9
AMES-Policy: 1/1/0/0
AMES-Positions: index/ephemeral/store/train
AMES-Values: 1=allow, 0=deny
AMES-Publisher: Catchlight
AMES-Domain: catchlightmag.com
AMES-Spec: https://openames.org/v0.9
```

### Example — Custom State

In the `custom` state, `AMES-Positions` and `AMES-Values` are omitted (there is no tuple to legend):

```text
AMES-Version: 0.9
AMES-Policy: custom
AMES-Publisher: Catchlight
AMES-Domain: catchlightmag.com
AMES-Spec: https://openames.org/v0.9
```

With an optional URL pointer to governing terms:

```text
AMES-Policy: custom https://catchlightmag.com/terms/ai-usage
```

!!! note
    A publisher governing its domain entirely through a custom permissions regime does not need this file. Where a custom regime already carries the publisher's terms, the `ames-ai.txt` declaration may be omitted in favor of that regime.

---

## 5.2 Permissions Cascade

AMES permissions resolve from the **most specific valid declaration** to the **broadest applicable valid declaration**:

```
Zone  →  Page  →  Site
```

Each broader layer applies only where a more-specific valid declaration is absent.

### Cascade Rules

| Scenario | Resolution |
|---|---|
| Valid zone-level declaration present | Zone declaration governs |
| No zone-level declaration | Page-level declaration governs |
| Malformed page-level declaration | Falls back to site-level (not directly to absence) |
| No site-level declaration | No AMES declaration exists for the content |
| Zone-level override, no page-level declaration | Zone override is **invalid** |
| Malformed zone-level tuple | Disregarded; zone governed by page-level declaration |

### Zone Override Dependency

Zone-level tuples **require** a page-level AMES native permission declaration to exist. A zone-level override (whether in an `.ax.md` zone marker or in a `data-ames-policy` attribute) is invalid when no page-level `ames-policy` declaration is present. A zone-level override is defined relative to a declared page-level baseline.
