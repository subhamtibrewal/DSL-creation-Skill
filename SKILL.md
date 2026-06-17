---
name: ma-dynamic-form-dsl
description: Authors and converts XMLish DSL forms for @melodyarc/ma_dynamic_form. Embeds portal form patterns (wizard pages, review cards, radioGroup navigation), button wiring, conditional logic, style sanitization, container-safe modals, and host integration. Use when creating .dsl.xml, converting forms, fixing Live Preview, or building dynamic forms for MarkupProvider/MarkupRenderer.
---

# ma_dynamic_form DSL Skill

Standalone skill for `@melodyarc/ma_dynamic_form`. **Do not read any external repo, AGENTS.md, or DSL_USER_GUIDE.md.** All rules live in this directory.

## File map

| File | When to read |
|------|----------------|
| [compatibility.md](compatibility.md) | Hard rules, pitfalls, host setup, style whitelist |
| [tags-reference.md](tags-reference.md) | Tag/prop lookup for `core:` and `html:` |
| [patterns.md](patterns.md) | Copy-paste XML templates |
| [portal-patterns.md](portal-patterns.md) | Production portal layouts, wizard pages, review cards |
| [examples.md](examples.md) | Complete working XML snippets |
| [scripts/validate-dsl.mjs](scripts/validate-dsl.mjs) | Lint before delivery |
| [scripts/compat-fix-dsl.mjs](scripts/compat-fix-dsl.mjs) | Convert incompatible XML |

## Standalone rule

Never assume the ma_dynamic_form source repo is open. Never run repo `npm test` or link to `.docs/`. Use only files in this skill folder.

## Workflow

```
Task → Read compatibility.md + tags-reference.md (or patterns.md)
     → Author new XML OR run compat-fix-dsl.mjs on foreign XML
     → Run validate-dsl.mjs
     → Fix failures using compatibility.md
     → Deliver .dsl.xml (+ host snippet if embedding)
```

### Author new form

1. Start from [patterns.md](patterns.md) or [portal-patterns.md](portal-patterns.md) for portal-style forms.
2. Seed defaults with `<state:set path="form.*" value="..." mode="unset" />`.
3. Bind inputs with `path="form.field"`; bind choice lists with `core:radioGroup path="..."`.
4. Use `setPath` / `setValue` on buttons that change UI state.
5. Use wizard `core:if equals` pages for multi-step flows; use container-safe modal only when overlay is needed.
6. Use `equals=""` pattern for button-based option lists (see patterns.md).
7. Escape dynamic text before injecting into XML (see portal-patterns.md).
8. Run validator before delivery.

### Convert foreign XML

```bash
node ~/.cursor/skills/ma-dynamic-form-dsl/scripts/compat-fix-dsl.mjs --input path/to/source.xml --output path/to/fixed.dsl.xml
node ~/.cursor/skills/ma-dynamic-form-dsl/scripts/validate-dsl.mjs path/to/fixed.dsl.xml
```

Review output manually for modals and large `notEquals` expansions.

## Pre-delivery checklist

- [ ] Buttons use `setPath` / `setValue` (not `path` / `value`)
- [ ] Empty initial state shows correct default UI (especially option lists)
- [ ] No reliance on stripped CSS (`border`, `fixed`, `zIndex`, `transform`, etc.)
- [ ] Modals use card `position:relative` + absolute overlay + `justifyContent:flex-end`
- [ ] `state:set` seeds every path read by `core:if`
- [ ] Multi-step flows use wizard `core:if` pages OR container-safe modal — not `position: fixed`
- [ ] Footer workflow uses `radioGroup` when that matches portal behavior
- [ ] Dynamic data escaped for XML (`&`, `"`, `<`, etc.)
- [ ] Surfaces use Mantine props (`withBorder`, `shadow`, `fw`, `c`) — not stripped inline CSS
- [ ] `validate-dsl.mjs` exits 0
- [ ] Host snippet from compatibility.md included if user embeds in React

## Quick anti-patterns

| Wrong | Right |
|-------|-------|
| `<core:button path="form.x" value="y">` | `<core:button setPath="form.x" setValue="y">` |
| `position:"fixed"` modal | Container-safe modal in patterns.md |
| `notEquals` only for empty-state options | `equals=""` block showing all options |
| `core:modal` / `core:drawer` | `core:box` + backdrop + `core:paper` |
| `renderUserMarkup()` only | `<MarkupRenderer source={...} />` inside `<MarkupProvider>` |
| `fontWeight` in style | `fw="700"` on `core:text` / button |

## Domain prefix

Prefer `core:` on Mantine-backed tags (`core:button`, `core:stack`, etc.). Control tags: `core:if` (or `<if>`), `core:each`, `state:set`.

Unprefixed tags like `<card>` resolve to `core:card` at runtime. Existing portal forms may omit the prefix; new forms should use `core:`.
