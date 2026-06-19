# ma-dynamic-form-dsl

Standalone skill for authoring and converting **XMLish DSL** forms for `@melodyarc/ma_dynamic_form`. All rules and scripts live in this folder — no repo dependency.

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Entry point — workflow and checklist |
| `compatibility.md` | Hard rules (buttons, styles, modals, host) |
| `tags-reference.md` | Tags and props |
| `portal-patterns.md` | Production portal layouts, header-band cards, surface defaults |
| `themes/thd.md` | THD accent colors (orange primary, blue links) |
| `patterns.md` | Copy-paste XML templates |
| `examples.md` | Full XML snippets |
| `DSL/confirm-issues.dsl.xml` | Confirm Issues — per-item header-band cards |
| `DSL/customer-request-classification.dsl.xml` | Intake form — textarea + classification |
| `DSL/out-of-scope.dsl.xml` | Display-only message card |
| `confirm-items-services.dsl.xml` | Confirm Items — order group cards + modals |
| `scripts/validate-dsl.mjs` | Lint before delivery |
| `scripts/compat-fix-dsl.mjs` | Convert incompatible XML |

## Scripts

```bash
node scripts/validate-dsl.mjs path/to/form.dsl.xml
node scripts/compat-fix-dsl.mjs --input source.xml --output fixed.dsl.xml
```

Requires Node.js 18+.

## Essentials

- **Default:** preserve all `form.*` paths and option values when converting — unless user asks to change them
- Buttons: `setPath` + `setValue` (not `path` / `value`)
- Radios: `path` on `radioGroup`, not each `radio`
- Multi-step: wizard `core:if` pages; modals = container-safe only
- Styles: use `withBorder`, `fw`, `c` — not stripped inline CSS; omit `shadow` unless requested
- Item/order cards: grey `#F5F5F5` header band + white body (`padding="0"` on card)
- THD theme: read `themes/thd.md` when user asks for THD / Home Depot styling
