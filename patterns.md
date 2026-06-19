# Copy-Paste Patterns

For portal-style layouts (wizard pages, review cards, order lines), see [portal-patterns.md](portal-patterns.md).

## 1. Minimal form shell

```xml
<state:set path="form.name" value="" mode="unset" />
<state:set path="form.email" value="" mode="unset" />
<state:set path="form.notes" value="" mode="unset" />

<core:card padding="md" radius="md">
  <core:stack gap="md">
    <core:title order="3">Contact form</core:title>

    <core:textInput label="Name" path="form.name" withAsterisk="true" />
    <core:textInput label="Email" path="form.email" withAsterisk="true" />
    <core:textarea label="Notes" path="form.notes" autosize="true" minRows="3" />

    <core:group gap="sm" justify="flex-end">
      <core:button variant="default" setPath="form.notes" setValue="">Clear notes</core:button>
      <core:button variant="filled" action="submitForm">Submit</core:button>
    </core:group>
  </core:stack>
</core:card>
```

## 2. Button state patch (open/close)

```xml
<state:set path="form.panel_open" value="no" mode="unset" />

<core:button variant="light" setPath="form.panel_open" setValue="yes">Open panel</core:button>
<core:button variant="subtle" setPath="form.panel_open" setValue="no">Close panel</core:button>

<core:if path="form.panel_open" equals="yes">
  <core:paper withBorder="true" p="md">
    <core:text>Panel content</core:text>
  </core:paper>
</core:if>
```

## 3. Button select row (option list)

Use explicit `equals=""` for empty initial state, then one block per selected value.

```xml
<state:set path="form.selected_full_name" value="" mode="unset" />

<core:stack gap="xs">
  <core:if path="form.selected_full_name" equals="">
    <core:button variant="subtle" setPath="form.selected_full_name" setValue="Sarah Marie Johnson" style='{"width":"100%","justifyContent":"flex-start"}'>Sarah Marie Johnson</core:button>
    <core:button variant="subtle" setPath="form.selected_full_name" setValue="Other Name" style='{"width":"100%","justifyContent":"flex-start"}'>Other Name</core:button>
  </core:if>

  <core:if path="form.selected_full_name" equals="Sarah Marie Johnson">
    <core:button variant="subtle" color="blue" setPath="form.selected_full_name" setValue="Sarah Marie Johnson" style='{"width":"100%","justifyContent":"flex-start","background":"rgba(5,23,255,0.08)"}'>Sarah Marie Johnson (selected)</core:button>
    <core:button variant="subtle" setPath="form.selected_full_name" setValue="Other Name" style='{"width":"100%","justifyContent":"flex-start"}'>Other Name</core:button>
  </core:if>
</core:stack>
```

## 3b. Radio-like option list (legacy / modal-safe)

Use explicit `equals=""` for empty initial state, then one block per selected value.

```xml
<state:set path="form.reason" value="" mode="unset" />

<core:stack gap="xs">
  <!-- Empty: show all options unselected -->
  <core:if path="form.reason" equals="">
    <core:button variant="subtle" setPath="form.reason" setValue="a" style='{"width":"100%","justifyContent":"flex-start"}'>Option A</core:button>
    <core:button variant="subtle" setPath="form.reason" setValue="b" style='{"width":"100%","justifyContent":"flex-start"}'>Option B</core:button>
  </core:if>

  <!-- Selected A -->
  <core:if path="form.reason" equals="a">
    <core:button variant="subtle" color="blue" setPath="form.reason" setValue="a" style='{"width":"100%","justifyContent":"flex-start","background":"rgba(5,23,255,0.08)"}'>Option A (selected)</core:button>
    <core:button variant="subtle" setPath="form.reason" setValue="b" style='{"width":"100%","justifyContent":"flex-start"}'>Option B</core:button>
  </core:if>

  <!-- Selected B -->
  <core:if path="form.reason" equals="b">
    <core:button variant="subtle" setPath="form.reason" setValue="a" style='{"width":"100%","justifyContent":"flex-start"}'>Option A</core:button>
    <core:button variant="subtle" color="blue" setPath="form.reason" setValue="b" style='{"width":"100%","justifyContent":"flex-start","background":"rgba(5,23,255,0.08)"}'>Option B (selected)</core:button>
  </core:if>
</core:stack>
```

## 4. Container-safe modal

```xml
<state:set path="form.modal_open" value="no" mode="unset" />
<state:set path="form.modal_choice" value="" mode="unset" />

<core:card padding="md" radius="md" style='{"position":"relative"}'>
  <core:stack gap="md">
    <core:title order="3">Main form</core:title>
    <core:textInput label="Field" path="form.field" />

    <core:button variant="light" color="blue" setPath="form.modal_open" setValue="yes">
      Open dialog
    </core:button>

    <core:if path="form.modal_open" equals="yes">
      <core:box style='{"position":"absolute","top":"0","left":"0","right":"0","bottom":"0","overflow":"auto","background":"rgba(0,0,0,0.55)","display":"flex","flexDirection":"column","justifyContent":"flex-end","alignItems":"center","padding":"24px"}'>
        <core:paper withBorder="true" radius="sm" p="lg" style='{"position":"relative","maxWidth":"680px","width":"100%","background":"#ffffff","flexShrink":0}'>
          <core:stack gap="md">
            <core:group justify="space-between" align="center">
              <core:title order="4">Choose reason</core:title>
              <core:button variant="subtle" size="sm" setPath="form.modal_open" setValue="no">Close</core:button>
            </core:group>

            <!-- Option list: use pattern 3 here -->

            <core:group gap="sm" justify="flex-end">
              <core:button variant="default" setPath="form.modal_open" setValue="no">Cancel</core:button>
              <core:button variant="filled" color="blue" setPath="form.modal_open" setValue="no">Confirm</core:button>
            </core:group>
          </core:stack>
        </core:paper>
      </core:box>
    </core:if>
  </core:stack>
</core:card>
```

Key points:
- Card has `position: relative`
- Backdrop is `position: absolute` filling the card
- **`justifyContent: flex-end`** — dialog appears near bottom, not vertically centered in a tall form

## 5. Menu swap section (without notEquals explosion)

For section type chooser with values `menu`, `full_name`, `email_address`, etc.:

```xml
<state:set path="form.section_type" value="menu" mode="unset" />

<!-- Menu chooser -->
<core:if path="form.section_type" equals="menu">
  <core:stack gap="xs">
    <core:button variant="subtle" setPath="form.section_type" setValue="full_name">Full name</core:button>
    <core:button variant="subtle" setPath="form.section_type" setValue="email_address">Email</core:button>
  </core:stack>
</core:if>

<core:if path="form.section_type" equals="full_name">
  <core:textInput label="Full name" path="form.full_name" />
  <core:button variant="subtle" setPath="form.section_type" setValue="menu">Back to menu</core:button>
</core:if>

<core:if path="form.section_type" equals="email_address">
  <core:textInput label="Email" path="form.email" />
  <core:button variant="subtle" setPath="form.section_type" setValue="menu">Back to menu</core:button>
</core:if>
```

Prefer explicit `equals` per value over nested `notEquals` filters.

## 7. Item issue card (header band + checkboxes)

One item per card. No shadow. See [DSL/confirm-issues.dsl.xml](DSL/confirm-issues.dsl.xml).

```xml
<state:set path="form.issue_item_damaged" value="false" mode="unset" />
<state:set path="form.issue_item_wrong" value="false" mode="unset" />
<state:set path="form.issue_item_missing" value="false" mode="unset" />

<core:card withBorder="true" padding="0" radius="md">
  <core:stack gap="0">
    <core:paper p="md" radius="0" style='{"background":"#F5F5F5"}'>
      <core:stack gap="xs">
        <core:text fw="600" size="sm">Dishwasher - Stainless Steel</core:text>
        <core:text size="xs" c="dimmed">SKU: DISH-2024-SS • Order #WX73924856</core:text>
      </core:stack>
    </core:paper>
    <core:paper p="md" radius="0">
      <core:stack gap="sm">
        <core:checkbox path="form.issue_item_damaged" label="Damaged item" />
        <core:checkbox path="form.issue_item_wrong" label="Received wrong item" />
        <core:checkbox path="form.issue_item_missing" label="Missing parts or accessories" />
      </core:stack>
    </core:paper>
  </core:stack>
</core:card>
```

## 8. Single-column intake sections

Bordered inset panels for field groups. See [DSL/customer-request-classification.dsl.xml](DSL/customer-request-classification.dsl.xml).

```xml
<core:paper withBorder="true" radius="md" p="md">
  <core:textarea label="Customer request" path="form.customer_request" autosize="true" minRows="3" withAsterisk="true" />
</core:paper>

<core:divider />

<core:stack gap="sm">
  <core:text fw="600" size="sm">Classify the customer's need</core:text>
  <core:paper withBorder="true" radius="md" p="md">
    <core:radioGroup path="form.need_classification" withAsterisk="true">
      <core:stack gap="xs">
        <core:radio value="post_order" label="Post order" size="md" />
        <core:radio value="pre_order" label="Pre order" size="md" />
      </core:stack>
    </core:radioGroup>
  </core:paper>
</core:stack>
```

## 9. Convert checklist (foreign XML → compatible DSL)

0. **Preserve** every `form.*` path, `state:set` value, and radio/checkbox option `value` unless the user asked to change them
1. Run `compat-fix-dsl.mjs --input ... --output ...`
2. Verify all `<core:button>` use `setPath`/`setValue`
3. Replace any remaining `position: fixed` with container-safe modal (pattern 4)
4. Fix option lists: add `equals=""` blocks for empty initial state
5. Replace stripped styles with Mantine props (`fw`, `withBorder`, `color`)
6. Add `state:set` for every path used in `core:if`
7. Add `position: relative` on root card if modal exists
8. Run `validate-dsl.mjs` — fix errors until exit 0
9. Test in host with `MarkupRenderer` (not `renderUserMarkup` alone)
