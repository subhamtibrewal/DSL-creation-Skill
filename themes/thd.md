# THD theme

**Read this when** the user asks for **THD theme**, **THD style**, or **Home Depot** styling.

Layout and structure still come from [portal-patterns.md](../portal-patterns.md). This file covers **color and accent** only.

## Tokens

| Role | Hex | DSL |
|------|-----|-----|
| **Primary** | `#f96302` | `color="orange"` on filled buttons and selected pills |
| **Link** | blue (portal default) | `variant="subtle" color="blue"` — View details, See other options |
| **Header band** | `#F5F5F5` | `style='{"background":"#F5F5F5"}'` on `core:paper` |
| **Section badge** | light blue | `color="blue" variant="light"` (unchanged) |

## Where to apply `color="orange"`

- Filled primary CTAs: Confirm, Confirm & Verify, Use Selected Items
- Selected state on button-row options (Verify Customer Select → Selected pattern)
- Do **not** rely on `color="orange"` on `core:checkbox` or `core:radio` — often ignored by the engine

## Where selection chrome comes from

Orange checkbox fill, peach selected-row background, and orange row borders are **host `MantineProvider` theme** on THD environments — not per-row `core:if` wrappers in DSL. See [compatibility.md](../compatibility.md).

## Surface defaults (THD forms)

- **No `shadow`** on cards unless the user explicitly asks
- `withBorder="true"` + `radius="md"` on cards
- Grey header band + white body for item and order cards (see [portal-patterns.md](../portal-patterns.md))

## Default vs THD

| | Default portal | THD |
|--|----------------|-----|
| Primary CTA | Host theme or omit `color` | `color="orange"` on filled buttons |
| Links | `color="blue"` | `color="blue"` (same) |
| Card shadows | Omit unless requested | Omit |
| Layout | [portal-patterns.md](../portal-patterns.md) | Same structure |
