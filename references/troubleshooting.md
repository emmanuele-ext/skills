# Build commands, symptoms, verification

## Which command after which change

| Changed | Command |
|---|---|
| Administration (JS, Vue template) | `bin/build-administration.sh` |
| Storefront SCSS or JS | `bin/build-storefront.sh` + `bin/console theme:compile` |
| Twig, PHP, `services.xml` | `bin/console cache:clear` |
| New plugin | `bin/console plugin:refresh` + `plugin:update` |
| `theme.json` | `bin/console theme:refresh` + `theme:compile` |

Dev watchers: `composer run watch:admin`, `composer run watch:storefront`.

Deploy: decide explicitly whether compiled admin assets (`Resources/public/administration/`) are versioned (recompile before every relevant commit) or built by the pipeline — document the choice in the README.

## Verification checklist — run in this order after every new element/block

1. **Picker** — appears in the right category, translated label, visible preview.
2. **Persistence** — fill fields, save, reload: values still there.
3. **Storefront** — public markup matches canvas, no Twig errors.
4. **Empty states** — no `<img src="">` / `<a href="">` when optional fields are empty.
5. **Language & responsive** — text follows sales channel language, columns collapse correctly at breakpoints.

## Common silent-failure symptoms

All of these fail silently — no exception, no log line. This is the cost of a convention-only contract (name string across 5 places).

| Symptom | Cause |
|---|---|
| Not in picker | Missing `previewComponent`, or `main.js` doesn't import the element folder |
| Config panel blank | `initElementConfig` not called in `created()` |
| Doesn't save | Missing `$emit('element-update', …)` |
| Block setting vanishes after save | Invented property outside `customFields` |
| Twig: "Impossible to access an attribute on a null variable" | Resolver never called `setData`, or not tagged with `shopware.cms.data_resolver` |
| Wrong-language text | Reading `element.config` instead of `element.translated.config` |
| New config key empty on existing layouts only | `defaultConfig` runs at slot creation only — add the same default to the Twig/SCSS fallback, or re-save each layout |
| Grid broken | Block template emits its own `.row` |
| Override has no effect | Path differs from original, or cache not cleared |
| Fix disappears after `composer install` | Change was made in `vendor/` |
