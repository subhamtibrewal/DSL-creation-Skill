---
name: ma-dynamic-form-dsl
description: Authors and converts XMLish DSL forms for @melodyarc/ma_dynamic_form using production portal patterns. Preserves form.* paths and option values on convert unless user requests changes. Covers header-band cards, THD theme accents, button wiring, radioGroup/checkbox binding, conditional logic, style sanitization, container-safe modals, and host integration. Use when creating .dsl.xml, converting forms, pasting DSL, fixing Live Preview, THD styling, or building dynamic forms for MarkupProvider/MarkupRenderer.
---

# ma_dynamic_form DSL Skill

Standalone skill for `@melodyarc/ma_dynamic_form`. **Do not read any external repo, AGENTS.md, or DSL_USER_GUIDE.md.** All rules live in this directory.

## File map

| File | When to read |
|------|----------------|
| [compatibility.md](compatibility.md) | Hard rules, pitfalls, host setup, style whitelist |
| [tags-reference.md](tags-reference.md) | Tag/prop lookup for `core:` and `html:` |
| [portal-patterns.md](portal-patterns.md) | **Primary reference** — layouts, header-band cards, surface defaults |
| [themes/thd.md](themes/thd.md) | THD accent colors — read when user asks for THD theme |
| [patterns.md](patterns.md) | Copy-paste XML templates |
| [examples.md](examples.md) | Complete working XML snippets |
| [DSL/confirm-issues.dsl.xml](DSL/confirm-issues.dsl.xml) | Confirm Issues — per-item header-band cards |
| [DSL/customer-request-classification.dsl.xml](DSL/customer-request-classification.dsl.xml) | Intake form — textarea + classification (paths preserved) |
| [DSL/out-of-scope.dsl.xml](DSL/out-of-scope.dsl.xml) | Display-only message card |
| [confirm-items-services.dsl.xml](confirm-items-services.dsl.xml) | Confirm Items — order group cards + modals |
| [scripts/validate-dsl.mjs](scripts/validate-dsl.mjs) | Lint before delivery |
| [scripts/compat-fix-dsl.mjs](scripts/compat-fix-dsl.mjs) | Convert incompatible XML |

## Standalone rule

Never assume the ma_dynamic_form source repo is open. Never run repo `npm test` or link to `.docs/`. Use only files in this skill folder.

## Preserve functionality and pathing (default)

**Unless the user explicitly asks to add, remove, or rename fields**, conversions and restyling must keep behavior and output pathing identical to the source form.

| Keep unchanged | OK to change (compatibility / DSL hygiene) |
|----------------|------------------------------------------|
| Every `path="form.*"` on inputs, `radioGroup`, `checkbox` | Unprefixed tags → `core:` prefix |
| Every `state:set` path and initial `value` | `setPath`/`setValue` on buttons (if source used `path`/`value`) |
| Radio/checkbox/select **values** and labels | `withBorder`, `radius`, typography props (`fw`, `size`, `c`) |
| `action` attributes (`submitForm`, etc.) | Strip unsafe inline CSS per [compatibility.md](compatibility.md) |
| Conditional logic (`core:if` paths and `equals` values) | Fix engine incompatibilities only |

**Do not** without explicit user request:

- Add or remove form fields, buttons, badges, or sections
- Rename or repath `form.*` keys (breaks host `pages` config and workflow output)
- Change radio/checkbox option `value` strings
- Restructure layout (e.g. inset `core:paper`, header bands, two-column) when the task is only “fix as per DSL” or “convert to DSL”

**Do** when user asks for portal layout, THD style, or a specific pattern from [portal-patterns.md](portal-patterns.md) — then apply structure from the matching canonical example while **preserving existing `form.*` paths and option values**.

## Workflow

```
Task → Read compatibility.md + tags-reference.md + portal-patterns.md
     → If user asks THD theme: also read themes/thd.md
     → Author new XML OR run compat-fix-dsl.mjs on foreign XML
     → Preserve all form.* paths and option values unless user requests changes
     → Match portal structure only when user asks for layout/style OR authoring net-new forms
     → Run validate-dsl.mjs
     → Fix failures using compatibility.md
     → Deliver .dsl.xml (+ host snippet if embedding)
```

### Author new form

1. Start from [portal-patterns.md](portal-patterns.md). Match form type to a canonical example:
   - Issue checkboxes per item → [DSL/confirm-issues.dsl.xml](DSL/confirm-issues.dsl.xml)
   - Order/item selection → [confirm-items-services.dsl.xml](confirm-items-services.dsl.xml)
   - Intake (textarea + classification) → [DSL/customer-request-classification.dsl.xml](DSL/customer-request-classification.dsl.xml)
   - Display-only message → [DSL/out-of-scope.dsl.xml](DSL/out-of-scope.dsl.xml)
2. If user asks for **THD theme**, read [themes/thd.md](themes/thd.md) for accent colors (layout unchanged unless also requested).
3. Seed defaults with `<state:set path="form.*" value="..." mode="unset" />`.
4. Bind inputs with `path="form.field"`; bind choice lists with `core:radioGroup path="..."`.
5. Use `setPath` / `setValue` on buttons that change UI state.
6. Use wizard `core:if equals` pages for multi-step flows; use container-safe modal only when overlay is needed.
7. Use `equals=""` pattern for button-based option lists (see [patterns.md](patterns.md)).
8. Escape dynamic text before injecting into XML (see [portal-patterns.md](portal-patterns.md)).
9. Run validator before delivery.

### Convert foreign XML

```bash
node ~/.cursor/skills/ma-dynamic-form-dsl/scripts/compat-fix-dsl.mjs --input path/to/source.xml --output path/to/fixed.dsl.xml
node ~/.cursor/skills/ma-dynamic-form-dsl/scripts/validate-dsl.mjs path/to/fixed.dsl.xml
```

Review output manually for modals and large `notEquals` expansions. **Do not restructure** unless the user asked for portal layout — only fix compatibility ([compatibility.md](compatibility.md)) and `core:` prefixes.

### Paste DSL from outside

1. Run `compat-fix-dsl.mjs` if needed.
2. Fix button wiring (`setPath`/`setValue`), radio `path` on group, and stripped styles per [compatibility.md](compatibility.md).
3. **Preserve all `form.*` paths and field values** — do not add badges, sections, or layout wrappers unless the user asked.
4. Restructure to portal patterns only when the user explicitly requests layout (e.g. “per skill”, “THD style”, “match Confirm Issues”).
5. Run `validate-dsl.mjs`.

## Pre-delivery checklist

- [ ] **All original `form.*` paths and option values preserved** (unless user asked to change them)
- [ ] Buttons use `setPath` / `setValue` (not `path` / `value`)
- [ ] Empty initial state shows correct default UI (especially option lists)
- [ ] No reliance on stripped CSS (`border`, `fixed`, `zIndex`, `transform`, etc.)
- [ ] Modals use card `position:relative` + absolute overlay + `justifyContent:flex-end`
- [ ] `state:set` seeds every path read by `core:if`
- [ ] Multi-step flows use wizard `core:if` pages OR container-safe modal — not `position: fixed`
- [ ] Footer workflow uses `radioGroup` when that matches portal behavior
- [ ] Dynamic data escaped for XML (`&`, `"`, `<`, etc.)
- [ ] Surfaces use Mantine props (`withBorder`, `fw`, `c`) — not stripped inline CSS; **omit `shadow` unless requested**
- [ ] Item/order lists use header-band cards (`padding="0"`, grey `#F5F5F5` header) — not one checkbox per nested card
- [ ] Structure matches [portal-patterns.md](portal-patterns.md)
- [ ] THD requests: [themes/thd.md](themes/thd.md) applied for accent; no custom selection wrappers
- [ ] `validate-dsl.mjs` exits 0
- [ ] Host snippet from compatibility.md included if user embeds in React

## Quick anti-patterns

| Wrong | Right |
|-------|-------|
| `<core:button path="form.x" value="y">` | `<core:button setPath="form.x" setValue="y">` |
| `position:"fixed"` modal | Container-safe modal in patterns.md |
| `notEquals` only for empty-state options | `equals=""` block showing all options |
| `path` on each `core:radio` | `path` on `core:radioGroup` |
| `core:modal` / `core:drawer` | `core:box` + backdrop + `core:paper` |
| `renderUserMarkup()` only | `<MarkupRenderer source={...} />` inside `<MarkupProvider>` |
| `fontWeight` in style | `fw="700"` on `core:text` / button |
| Custom theme/color layers not in portal | Standard `core:checkbox`, `core:radioGroup`, Mantine props |
| Adding fields/badges/layout on “convert to DSL” | Only `core:` prefix + compatibility fixes; preserve pathing |

## Domain prefix

Prefer `core:` on Mantine-backed tags (`core:button`, `core:stack`, etc.). Control tags: `core:if` (or `<if>`), `core:each`, `state:set`.

Unprefixed tags like `<card>` resolve to `core:card` at runtime. Existing portal forms may omit the prefix; new forms should use `core:`.
