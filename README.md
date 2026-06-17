# ma-dynamic-form-dsl

Standalone skill for authoring and converting **XMLish DSL** forms for `@melodyarc/ma_dynamic_form`. All rules and scripts live in this folder — no repo dependency.

## Files


| File                                 | Purpose                                    |
| ------------------------------------ | ------------------------------------------ |
| `SKILL.md`                           | Entry point — workflow and checklist       |
| `compatibility.md`                   | Hard rules (buttons, styles, modals, host) |
| `tags-reference.md`                  | Tags and props                             |
| `patterns.md` / `portal-patterns.md` | Templates and portal layouts               |
| `examples.md`                        | Full XML snippets                          |
| `scripts/validate-dsl.mjs`           | Lint before delivery                       |
| `scripts/compat-fix-dsl.mjs`         | Convert incompatible XML                   |


Optional: add `ui-defaults.md` and reference it from `SKILL.md` for a default UI profile (unless the user overrides).

## Scripts

```bash
node scripts/validate-dsl.mjs path/to/form.dsl.xml
node scripts/compat-fix-dsl.mjs --input source.xml --output fixed.dsl.xml
```

Requires Node.js 18+.

## Essentials

- Buttons: `setPath` + `setValue` (not `path` / `value`)
- Radios: `path` on `radioGroup`, not each `radio`
- Multi-step: wizard `core:if` pages; modals = container-safe only
- Styles: use `withBorder`, `shadow`, `fw`, `c` — not stripped inline CSS

Details: `SKILL.md`, `compatibility.md`.