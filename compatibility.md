# ma_dynamic_form Compatibility Rules

Engine: `@melodyarc/ma_dynamic_form` with `@mantine/core` v7+.

## Preserve pathing on convert

When fixing or converting existing DSL, **do not rename `form.*` paths, change option `value` strings, or add/remove fields** unless the user explicitly requests it. Allowed changes: `core:` prefix, button `setPath`/`setValue`, style sanitization, and other engine compatibility fixes ([SKILL.md](SKILL.md#preserve-functionality-and-pathing-default)).

## Button state wiring

Buttons that update runtime state **must** use:

```xml
<core:button setPath="form.field" setValue="some_value">Label</core:button>
```

**Do not** use `path` / `value` on buttons — those attributes are stripped and clicks do nothing.

Optional host actions:

```xml
<core:button action="submitForm" setPath="ui.step" setValue="done">Save</core:button>
```

Use `setValuePath` when the value comes from existing state:

```xml
<core:button setPath="ui.selected" setValuePath="item.id">Pick</core:button>
```

## Input binding

Form inputs bind with `path`:

```xml
<core:textInput label="Name" path="form.name" />
<core:textarea label="Notes" path="form.notes" />
```

## state:set defaults

Seed state at the top of the form:

```xml
<state:set path="form.name" value="" mode="unset" />
<state:set path="form.modal_open" value="no" mode="unset" />
```

- `mode="unset"` — only writes if path is currently undefined (safe on re-render).
- `mode="always"` — overwrites every time source is first applied.

**Important:** `state:set` runs inside `MarkupRenderer` on mount (useEffect). It does **not** run when calling `renderUserMarkup()` directly without `MarkupRenderer`.

## core:if conditionals

Supported attributes on `<core:if path="...">`:

| Attribute | Meaning |
|-----------|---------|
| `equals="value"` | path === value (string compare after coercion) |
| `notEquals="value"` | path !== value |
| `exists="true"` | path is not null/undefined |
| `exists="false"` | path is null/undefined |
| `truthy="true"` | Boolean(path) is true |
| `truthy="false"` | Boolean(path) is false |
| `stringLengthGreaterThan="N"` | String(path).length > N |

### Empty-string pitfall

Radio-like UI often uses:

```xml
<core:if path="form.choice" notEquals="option_a">...</core:if>
```

When `form.choice` is `""`, older engines without `notEquals` treat the block as falsy (hidden). **Safe pattern:** use an explicit empty-state block:

```xml
<core:if path="form.choice" equals="">
  <!-- show ALL unselected options here -->
</core:if>
<core:if path="form.choice" equals="option_a">
  <!-- show selected option_a + unselected others -->
</core:if>
```

See [patterns.md](patterns.md) for the full template.

### notEquals expansion warning

Blindly expanding nested `notEquals` (e.g. menu filters) can explode file size (40k+ lines). Prefer native `notEquals` or hand-crafted `equals` domains. Use `compat-fix-dsl.mjs` for controlled expansion.

## Style sanitization

Inline `style='{"key":"value"}'` JSON is filtered. Only these keys survive:

**Allowed:** `margin`, `marginTop`, `marginBottom`, `marginLeft`, `marginRight`, `padding`, `paddingTop`, `paddingBottom`, `paddingLeft`, `paddingRight`, `display`, `gap`, `width`, `height`, `maxWidth`, `minWidth`, `position`, `top`, `right`, `bottom`, `left`, `inset`, `overflow`, `overflowX`, `overflowY`, `borderRadius`, `background`, `backgroundColor`, `color`, `justifyContent`, `alignItems`, `flexDirection`, `flex`

**Position rule:** only `static`, `relative`, `absolute` — **`fixed` is dropped**.

**Commonly stripped (do not rely on):** `border`, `boxShadow`, `fontWeight`, `lineHeight`, `transform`, `zIndex`, `whiteSpace`, `wordBreak`, `overflowWrap`, `paddingLeft`, `paddingRight`

**Use Mantine props instead:**

| Stripped style | Use prop |
|----------------|----------|
| `fontWeight: 700` | `fw="700"` |
| `lineHeight: 1.45` | `lh="1.45"` |
| borders | `withBorder="true"` |
| box shadow | `shadow="md"` on `core:card` / `core:paper` |
| colors | `c="dimmed"`, `color="blue"` on buttons/badges where needed |

**Allowed inline backgrounds** used in portal review forms: `style='{"background":"rgba(0,0,0,0.02)"}'` for inset panels, `style='{"background":"rgba(255,0,0,0.03)"}'` for rejection areas.

## Forbidden / unavailable tags

Do **not** use:

- `core:modal`
- `core:drawer`
- `core:portal`
- `core:affix`
- `core:appShell`

Overlays must stay **container-scoped**. Use the modal pattern in [patterns.md](patterns.md).

## Container-safe modals

Never use `position: fixed` for dialogs. Pattern:

1. Root `<core:card style='{"position":"relative"}'>`
2. Modal gated by `<core:if path="form.modal_open" equals="yes">`
3. Absolute overlay box covering card with dimmed background
4. **`justifyContent: flex-end`** — anchors dialog near footer/trigger (centering in tall forms hides the title)

Full template in [patterns.md](patterns.md).

## Host app integration

Minimum React setup:

```tsx
import React from "react";
import { MantineProvider } from "@mantine/core";
import "@mantine/core/styles.css";
import {
  MarkupProvider,
  MarkupRenderer,
} from "@melodyarc/ma_dynamic_form";

export function DynamicForm({ source }: { source: string }) {
  return (
    <MantineProvider>
      <MarkupProvider
        options={{
          limits: { maxNodes: 20000, maxDepth: 90 },
        }}
      >
        <MarkupRenderer source={source} />
      </MarkupProvider>
    </MantineProvider>
  );
}
```

Requirements:

- **MantineProvider** — Mantine components need theme context.
- **MarkupProvider** — supplies runtime state, registry, options.
- **MarkupRenderer** — parses XML, applies `state:set`, re-renders on state changes.

Do **not** use `renderUserMarkup()` alone for interactive forms — button clicks and `state:set` need the provider/renderer lifecycle.

### Render limits

Default limits: `maxNodes: 20000`, `maxDepth: 90`. If rendering stops early (partial UI), increase limits or reduce XML size.

Large forms may have 40k+ lines of XML but only ~300–500 active nodes when conditionals filter content.

## Tag domains

- `core:*` — Mantine-backed components (preferred)
- Unprefixed tags (e.g. `<card>`, `<stack>`) — resolve to `core:*` when no `html:*` match exists
- `html:*` — safe HTML subset (`div`, `span`, `p`, etc.) — styles still sanitized
- `state:set` — initialization directive (not rendered)
- `core:if` or `<if>` — conditional render
- `core:each` — loops

Unknown domains/tags throw in strict mode or render nothing.

## Choice inputs

Use `core:radioGroup` with `path` on the group. Child `core:radio` elements use `value` and `label` only.

For footer workflow and yes/no decisions, `radioGroup` is the standard portal pattern. See [portal-patterns.md](portal-patterns.md).

## Dynamic content

When building XML from runtime data (order lines, decoy answers, messages), escape attribute and text values: `&`, `"`, `'`, `<`, `>`.

## Common failure symptoms

| Symptom | Likely cause |
|---------|----------------|
| Buttons render but state never changes | `path`/`value` instead of `setPath`/`setValue` |
| Section headers show, options hidden | `notEquals` + empty string, or missing `equals=""` block |
| Modal opens but title/options clipped | `justifyContent: center` in tall form — use `flex-end` |
| Modal invisible | `position: fixed` stripped; use container-safe pattern |
| Radio group does not bind | `path` on `radioGroup`, not on each `radio` |
| Nothing renders after load | `maxNodes` exceeded or invalid XML |
| state:set ignored | Not using `MarkupRenderer` |
