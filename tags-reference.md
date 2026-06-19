# Tag Reference (Form Authoring)

Props accept string booleans (`"true"`, `"false"`) and JSON for `style` and `data`.

Prefer `core:` prefix. Unprefixed tags (e.g. `<card>`) resolve to `core:card` at runtime.

## Layout

### core:card
Form root container. Add `style='{"position":"relative"}'` when using container-safe modals.

```xml
<core:card padding="lg" radius="md" withBorder="true" shadow="sm">...</core:card>
```
Props: `padding`, `radius`, `withBorder`, `shadow`, `style`

### core:stack
Vertical flex layout.

```xml
<core:stack gap="md">...</core:stack>
```
Props: `gap`, `align`, `justify`, `style`

### core:group
Horizontal flex layout.

```xml
<core:group gap="sm" justify="space-between" align="center" wrap="nowrap">...</core:group>
```
Props: `gap`, `align`, `justify`, `wrap`, `grow`, `style`

### core:paper
Surface panel; nested inset blocks in review forms.

```xml
<core:paper withBorder="true" radius="sm" p="md" shadow="md">...</core:paper>
```
Props: `withBorder`, `radius`, `p`, `shadow`, `style`

### core:box
Generic block; modal backdrop.

```xml
<core:box style='{"display":"flex","gap":"8px"}'>...</core:box>
```

### core:simpleGrid
```xml
<core:simpleGrid cols="2" spacing="md">...</core:simpleGrid>
```

### core:divider
Section separator.

```xml
<core:divider />
```

## Text

### core:text
```xml
<core:text size="sm" c="dimmed" fw="600">Helper text</core:text>
```
Props: `size`, `c`, `fw`, `lh`, `ta`, `style`

### core:title
```xml
<core:title order="3">Section title</core:title>
```

### core:badge
```xml
<core:badge color="blue" variant="light">Request selection</core:badge>
<core:badge variant="outline">Qty ordered: 2</core:badge>
```
Props: `color`, `variant`, `size`, `style`

### core:code
Inline identifier display.

```xml
<core:code>order-12345</core:code>
```

## Inputs

Bind value fields with `path="form.field"`.

### core:textInput
```xml
<core:textInput label="Email" path="form.email" placeholder="you@example.com" withAsterisk="true" />
```

### core:textarea
```xml
<core:textarea label="Notes" path="form.notes" autosize="true" minRows="3" withAsterisk="true" />
```

### core:select
```xml
<core:select label="Answer" path="form.answer" searchable="true" clearable="true"
  data='[{"value":"a","label":"Option A"}]' />
```

### core:multiSelect
```xml
<core:multiSelect label="Select actions" path="form.actions" searchable="true" clearable="true"
  data='[{"value":"refund","label":"Refund"}]' />
```

### core:numberInput
```xml
<core:numberInput label="Quantity" path="form.qty" min="1" max="10" clampBehavior="strict" />
```

### core:checkbox
```xml
<core:checkbox path="form.line_include" label=" " />
```

### core:switch
```xml
<core:switch label="Enable" path="form.enabled" />
```

### core:radioGroup + core:radio
**Path goes on the group**, not individual radios.

```xml
<core:radioGroup path="form.footer_action" withAsterisk="true">
  <core:stack gap="xs">
    <core:radio value="continue" label="Continue" size="md" />
    <core:radio value="back" label="Back" size="md" />
  </core:stack>
</core:radioGroup>
```

`path` goes on the group, not each radio. See [portal-patterns.md](portal-patterns.md).

## Disclosure

### core:accordion
Collapsible sections (e.g. order line lists).

```xml
<core:accordion variant="contained" radius="md" chevronPosition="right">
  <core:accordionItem value="lines">
    <core:accordionControl>
      <core:text fw="600" size="sm">Line items (3)</core:text>
    </core:accordionControl>
    <core:accordionPanel>
      <core:stack gap="md">...</core:stack>
    </core:accordionPanel>
  </core:accordionItem>
</core:accordion>
```

Child tags: `core:accordionItem`, `core:accordionControl`, `core:accordionPanel`.

## Actions

### core:button
State updates: `setPath` + `setValue`. **Not** `path`/`value`.

```xml
<core:button variant="filled" color="blue" setPath="form.step" setValue="2">Next</core:button>
<core:button action="submitForm">Submit</core:button>
```

Props: `variant`, `color`, `size`, `radius`, `setPath`, `setValue`, `setValuePath`, `action`, `disabled`, `style`

## Control flow

### core:if (or `<if>`)
```xml
<core:if path="form.show_extra" equals="yes">
  <core:textInput label="Extra" path="form.extra" />
</core:if>
```

See [compatibility.md](compatibility.md) for operators.

### core:each
```xml
<core:each path="items" item="item" index="idx">
  <core:text>{item.name}</core:text>
</core:each>
```

### state:set
```xml
<state:set path="form.status" value="" mode="unset" />
```

## Overlays (container-scoped)

### core:overlay
Parent must have `position: relative` or `absolute`.

### core:tooltip / core:popover / core:menu
Container-scoped only. See [compatibility.md](compatibility.md).

## HTML (safe subset)

### html:div / html:span
Styles still sanitized. Prefer `core:box` / `core:text`.

## Forbidden patterns

| Pattern | Why |
|---------|-----|
| `<core:modal>` | Not in registry |
| `<core:drawer>` | Not in registry |
| `<core:portal>` | Not in registry |
| `style='{"position":"fixed"}'` | Stripped / broken |
| `<core:button path="..." value="...">` | Ignored |
| `path` on `<core:radio>` | Use `path` on `radioGroup` instead |
| Nested `notEquals` without plan | XML explosion |

## Parent restrictions (common)

- `core:accordionItem` — parent must be `core:accordion`
- `core:accordionControl` / `core:accordionPanel` — parent `core:accordion` or `core:accordionItem`
- Modal backdrop — inside `core:card` with `position: relative`
