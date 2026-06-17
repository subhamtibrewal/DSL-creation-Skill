# Portal Form Patterns

Patterns observed in production portal forms. Use `core:` prefix on new forms. Unprefixed tags (`<card>`, `<if>`) also resolve to `core:` at runtime, but prefer `core:` for clarity.

## Form shell (header + body + footer)

Outer structure used across order selection, plan review, and verification flows:

```xml
<core:card padding="lg">
  <core:stack gap="lg">
    <!-- Header -->
    <core:group justify="space-between" align="center">
      <core:stack gap="xs">
        <core:title order="3">Form title</core:title>
        <core:text size="sm" c="dimmed">Short instructions.</core:text>
      </core:stack>
      <core:badge color="blue" variant="light">Section label</core:badge>
    </core:group>

    <!-- Body sections -->
    <core:stack gap="md">...</core:stack>

    <!-- Footer navigation (see below) -->
    <core:divider />
    ...
  </core:stack>
</core:card>
```

Spacing convention: `gap="lg"` outer, `gap="md"` sections, `gap="xs"` radio lists.

## Wizard pages via state (not modals)

Multi-step portal forms switch visible blocks with `core:if` on a single state path. Examples from production: `form.allow_send`, `form.planDecision`, `form.footer_action`.

```xml
<state:set path="form.allow_send" value="yes" mode="unset" />

<core:if path="form.allow_send" equals="yes">
  <core:card withBorder="true" radius="xl" padding="xl">
    <core:stack gap="md">
      <core:text fw="600">Step one content</core:text>
      <core:radioGroup path="form.allow_send">
        <core:stack gap="xs">
          <core:radio value="no" label="No" />
          <core:radio value="advanced_options" label="More options" />
        </core:stack>
      </core:radioGroup>
    </core:stack>
  </core:card>
</core:if>

<core:if path="form.allow_send" equals="no">
  <core:card withBorder="true" radius="xl" padding="xl">
    <core:stack gap="md">
      <core:text fw="600">Step two content</core:text>
      <core:radioGroup path="form.allow_send">
        <core:radio value="yes" label="Back" />
      </core:radioGroup>
    </core:stack>
  </core:card>
</core:if>
```

Seed the initial page value with `state:set` before the conditional blocks.

## Footer navigation with radioGroup

Portal forms often route workflow with a footer `radioGroup`, not buttons:

```xml
<state:set path="form.footer_action" value="continue" mode="unset" />

<core:divider />

<core:radioGroup path="form.footer_action">
  <core:stack gap="xs">
    <core:radio value="continue" label="Continue with selections" />
    <core:radio value="manual_authenticate_user" label="Return to customer verification" />
    <core:radio value="manual_request_flow" label="Choose different items" />
  </core:stack>
</core:radioGroup>
```

## Question card (verification)

Used for account verification Q&A. Bind answers on `radioGroup`; put `path` on the group, not each radio.

```xml
<core:card withBorder="true" padding="md" radius="md">
  <core:stack gap="sm">
    <core:text fw="600">What name is on the account?</core:text>
    <core:radioGroup path="form.full_name">
      <core:stack gap="xs">
        <core:radio value="Decoy A" label="Decoy A" />
        <core:radio value="Decoy B" label="Decoy B" />
        <core:radio value="Jane Doe" label="Jane Doe" />
      </core:stack>
    </core:radioGroup>
  </core:stack>
</core:card>
```

Optional section gating with UI flags:

```xml
<state:set path="form.ui.show_email" value="true" mode="unset" />

<core:if path="form.ui.show_email" equals="true">
  <!-- question card for email -->
</core:if>
```

Channel-specific blocks (phone shows decoys, chat shows real answer only) use `core:if path="form.ui.channel" equals="phone"` / `equals="chat"`.

## Review / message card

Read-only message plus yes/no decision. Use Mantine props for surfaces; avoid inline `border` or `boxShadow` (stripped by sanitizer).

```xml
<state:set path="form.allow_send" value="" mode="unset" />
<state:set path="form.message_rejection_reason" value="" mode="unset" />

<core:card padding="lg">
  <core:paper withBorder="true" radius="md" p="xl" shadow="md">
    <core:stack gap="lg">
      <core:badge variant="light" color="blue">Customer message</core:badge>
      <core:divider />

      <core:paper p="md" style='{"background":"rgba(0,0,0,0.02)"}'>
        <core:stack gap="xs">
          <core:text size="xs" c="dimmed">Message</core:text>
          <core:text size="sm">Message body here.</core:text>
        </core:stack>
      </core:paper>

      <core:text size="sm" c="dimmed">Purpose or context text.</core:text>
      <core:divider />

      <core:stack gap="sm">
        <core:text size="sm" fw="600">Can I proceed with this?</core:text>
        <core:radioGroup path="form.allow_send" withAsterisk="true">
          <core:stack gap="xs">
            <core:radio value="yes" label="Yes" size="md" />
            <core:radio value="no" label="No" size="md" />
          </core:stack>
        </core:radioGroup>

        <core:if path="form.allow_send" equals="no">
          <core:paper p="md" style='{"background":"rgba(255,0,0,0.03)"}'>
            <core:textarea
              label="Reason (required)"
              path="form.message_rejection_reason"
              withAsterisk="true"
              autosize="true"
              minRows="5"
            />
          </core:paper>
        </core:if>
      </core:stack>
    </core:stack>
  </core:paper>
</core:card>
```

## Order line item card

From order selection flows: checkbox + line details + quantity input.

```xml
<core:card withBorder="true" padding="md" radius="md">
  <core:stack gap="sm">
    <core:group justify="space-between" align="flex-start" wrap="nowrap">
      <core:group gap="sm" align="flex-start" wrap="nowrap">
        <core:checkbox path="form.line_include_order1_line1" label=" " />
        <core:stack gap="xs">
          <core:text fw="600" size="sm">Line #1</core:text>
          <core:text size="sm">Item description</core:text>
          <core:text size="xs" c="dimmed">Item #12345 · Status</core:text>
        </core:stack>
      </core:group>
      <core:badge variant="outline">Qty ordered: 2</core:badge>
    </core:group>

    <core:numberInput
      label="Quantity for this request"
      path="form.qty_order1_line1"
      min="1"
      max="2"
      clampBehavior="strict"
    />
  </core:stack>
</core:card>
```

Collapsible line lists use `core:accordion` wrapping multiple line cards (see tags-reference.md).

## Action selection per line

```xml
<core:multiSelect
  label="Select actions"
  path="form.actions_order1_line1"
  placeholder="Choose one or more actions"
  searchable="true"
  clearable="true"
  data='[{"value":"refund","label":"Refund"},{"value":"replace","label":"Replace"}]'
/>
```

## Ask field choice (radio vs select vs textarea)

When generating questions dynamically:

- **4 or fewer** short options (each label ≤ 40 chars) → `core:radioGroup` + `core:radio`
- **More options** → `core:select` with JSON `data='[{"value":"...","label":"..."}]'`
- **No options** → `core:textarea`

## Dynamic XML from data

When injecting user or API data into attributes or text, escape XML first:

```
& → &amp;   " → &quot;   ' → &apos;   < → &lt;   > → &gt;
```

Apply escaping to all dynamic attribute values and text content before building the XML string.

## UI flags and common state paths

| Path | Typical use |
|------|-------------|
| `form.footer_action` | Footer workflow choice |
| `form.allow_send` | Review / wizard page router |
| `form.ui.show_email` | Toggle email verification section |
| `form.ui.show_phone` | Toggle phone section |
| `form.ui.channel` | `phone` vs `chat` conditional content |
| `form.ui.address_layout` | `separate` vs `combined` address blocks |
| `form.line_include_*` | Per-line checkbox |
| `form.qty_*` | Per-line quantity |
| `form.actions_*` | Per-line multiSelect |

Seed every path used in `core:if` with `state:set mode="unset"`.
