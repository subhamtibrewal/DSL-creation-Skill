# Complete Examples

Self-contained XML snippets. Copy wholesale into a `.dsl.xml` file.

## Example A: 3-field form with submit (~40 lines)

```xml
<state:set path="form.first_name" value="" mode="unset" />
<state:set path="form.last_name" value="" mode="unset" />
<state:set path="form.email" value="" mode="unset" />

<core:card padding="md" radius="md">
  <core:stack gap="md">
    <core:title order="3">Customer details</core:title>
    <core:text size="sm" c="dimmed">Enter the customer's contact information.</core:text>

    <core:group gap="md" grow="true">
      <core:textInput
        label="First name"
        path="form.first_name"
        placeholder="Jane"
        withAsterisk="true"
      />
      <core:textInput
        label="Last name"
        path="form.last_name"
        placeholder="Doe"
        withAsterisk="true"
      />
    </core:group>

    <core:textInput
      label="Email address"
      path="form.email"
      placeholder="jane@example.com"
      withAsterisk="true"
    />

    <core:group gap="sm" justify="flex-end">
      <core:button variant="default" action="cancelForm">Cancel</core:button>
      <core:button variant="filled" color="blue" action="submitForm">Save customer</core:button>
    </core:group>
  </core:stack>
</core:card>
```

## Example B: Modal with 3 reason options (~80 lines)

```xml
<state:set path="form.verified" value="pending" mode="unset" />
<state:set path="form.modal_open" value="no" mode="unset" />
<state:set path="form.unable_reason" value="" mode="unset" />

<core:card padding="md" radius="md" style='{"position":"relative"}'>
  <core:stack gap="md">
    <core:title order="3">Verification</core:title>

    <core:if path="form.verified" equals="pending">
      <core:text size="sm">Customer verification is pending.</core:text>
      <core:button variant="light" color="orange" setPath="form.modal_open" setValue="yes">
        Unable to verify
      </core:button>
    </core:if>

    <core:if path="form.verified" equals="yes">
      <core:badge color="green" variant="light">Verified</core:badge>
    </core:if>

    <core:if path="form.modal_open" equals="yes">
      <core:box style='{"position":"absolute","top":"0","left":"0","right":"0","bottom":"0","overflow":"auto","background":"rgba(0,0,0,0.55)","display":"flex","flexDirection":"column","justifyContent":"flex-end","alignItems":"center","padding":"24px"}'>
        <core:paper withBorder="true" radius="sm" p="lg" style='{"position":"relative","maxWidth":"560px","width":"100%","background":"#ffffff","flexShrink":0}'>
          <core:stack gap="md">
            <core:group justify="space-between" align="center">
              <core:title order="4">Why unable to verify?</core:title>
              <core:button variant="subtle" size="sm" setPath="form.modal_open" setValue="no">✕</core:button>
            </core:group>

            <core:stack gap="xs">
              <core:if path="form.unable_reason" equals="">
                <core:button variant="subtle" setPath="form.unable_reason" setValue="missing_info" style='{"width":"100%","justifyContent":"flex-start"}'>Missing required information</core:button>
                <core:button variant="subtle" setPath="form.unable_reason" setValue="no_match" style='{"width":"100%","justifyContent":"flex-start"}'>Information does not match</core:button>
                <core:button variant="subtle" setPath="form.unable_reason" setValue="other" style='{"width":"100%","justifyContent":"flex-start"}'>Other</core:button>
              </core:if>

              <core:if path="form.unable_reason" equals="missing_info">
                <core:button variant="subtle" color="orange" setPath="form.unable_reason" setValue="missing_info" style='{"width":"100%","justifyContent":"flex-start","background":"#fff4ed"}'>Missing required information</core:button>
                <core:button variant="subtle" setPath="form.unable_reason" setValue="no_match" style='{"width":"100%","justifyContent":"flex-start"}'>Information does not match</core:button>
                <core:button variant="subtle" setPath="form.unable_reason" setValue="other" style='{"width":"100%","justifyContent":"flex-start"}'>Other</core:button>
              </core:if>

              <core:if path="form.unable_reason" equals="no_match">
                <core:button variant="subtle" setPath="form.unable_reason" setValue="missing_info" style='{"width":"100%","justifyContent":"flex-start"}'>Missing required information</core:button>
                <core:button variant="subtle" color="orange" setPath="form.unable_reason" setValue="no_match" style='{"width":"100%","justifyContent":"flex-start","background":"#fff4ed"}'>Information does not match</core:button>
                <core:button variant="subtle" setPath="form.unable_reason" setValue="other" style='{"width":"100%","justifyContent":"flex-start"}'>Other</core:button>
              </core:if>

              <core:if path="form.unable_reason" equals="other">
                <core:button variant="subtle" setPath="form.unable_reason" setValue="missing_info" style='{"width":"100%","justifyContent":"flex-start"}'>Missing required information</core:button>
                <core:button variant="subtle" setPath="form.unable_reason" setValue="no_match" style='{"width":"100%","justifyContent":"flex-start"}'>Information does not match</core:button>
                <core:button variant="subtle" color="orange" setPath="form.unable_reason" setValue="other" style='{"width":"100%","justifyContent":"flex-start","background":"#fff4ed"}'>Other</core:button>
                <core:textarea label="Details" path="form.unable_details" autosize="true" minRows="2" withAsterisk="true" />
              </core:if>
            </core:stack>

            <core:group gap="sm" justify="flex-end">
              <core:button variant="default" setPath="form.modal_open" setValue="no">Cancel</core:button>
              <core:button variant="filled" color="orange" setPath="form.verified" setValue="no" action="submitForm">Confirm</core:button>
            </core:group>
          </core:stack>
        </core:paper>
      </core:box>
    </core:if>
  </core:stack>
</core:card>
```

## Example C: Conditional section with core:if equals (~30 lines)

```xml
<state:set path="form.account_type" value="personal" mode="unset" />
<state:set path="form.company_name" value="" mode="unset" />

<core:card padding="md" radius="md">
  <core:stack gap="md">
    <core:title order="3">Account setup</core:title>

    <core:group gap="sm">
      <core:button
        variant="light"
        color="blue"
        setPath="form.account_type"
        setValue="personal"
      >Personal</core:button>
      <core:button
        variant="light"
        color="blue"
        setPath="form.account_type"
        setValue="business"
      >Business</core:button>
    </core:group>

    <core:if path="form.account_type" equals="personal">
      <core:text size="sm" c="dimmed">Personal accounts use your individual details.</core:text>
      <core:textInput label="Full name" path="form.full_name" withAsterisk="true" />
    </core:if>

    <core:if path="form.account_type" equals="business">
      <core:text size="sm" c="dimmed">Business accounts require a company name.</core:text>
      <core:textInput label="Company name" path="form.company_name" withAsterisk="true" />
      <core:textInput label="Tax ID" path="form.tax_id" />
    </core:if>

    <core:button variant="filled" action="submitForm">Continue</core:button>
  </core:stack>
</core:card>
```

## Example D: Review message with yes/no (~45 lines)

Portal-style review card. Uses allowed styling only.

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
          <core:text size="sm">Hello, your order is ready for pickup.</core:text>
        </core:stack>
      </core:paper>

      <core:text size="sm" c="dimmed">Confirm this message before sending to the customer.</core:text>
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
