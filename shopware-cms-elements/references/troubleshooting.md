# Build commands, symptoms, verification

## Which command after which change

| Changed | Command |
|---|---|
| Administration (JS, Vue template) | `bin/build-administration.sh` |
| Storefront SCSS only | `bin/console theme:compile` |
| Storefront JS | `bin/build-storefront.sh` (already runs `theme:compile`) |
| Twig, PHP, `services.xml` | `bin/console cache:clear` |
| New plugin | `bin/console plugin:refresh` + `plugin:install --activate <Name>` |
| Plugin version bump (new migration, `update()` logic) | `bin/console plugin:update <Name>` |
| `theme.json` | `bin/console theme:refresh` + `theme:compile` |

Dev watchers: `bin/watch-administration.sh`, `bin/watch-storefront.sh`.

Deploy: decide explicitly whether compiled admin assets (`Resources/public/administration/`) are versioned (recompile before every relevant commit) or built by the pipeline — document the choice in the README.

## Verification checklist — run in this order after every new element/block

1. **Picker** — appears in the right category, translated label, visible preview.
2. **Persistence** — fill fields, save, reload: values still there.
3. **Storefront** — public markup matches canvas, no Twig errors.
4. **Empty states** — no `<img src="">` / `<a href="">` when optional fields are empty.
5. **Language & responsive** — text follows sales channel language, columns collapse correctly at breakpoints.

## Common silent-failure symptoms

All of these fail silently — no exception, no log line. This is the cost of a convention-only contract (one name string across admin, Twig and PHP).

| Symptom | Cause |
|---|---|
| Not in picker | Missing `previewComponent`, or `main.js` doesn't import the element folder |
| Config panel blank | `initElementConfig` not called in `created()` |
| Change made from a product's Layout tab is lost (layout editor saves fine) | Missing `$emit('element-update', …)` — `sw-cms-page-form` only picks up changes through that event |
| Block sidebar tab shows a raw snippet key | Custom block `category` without the `apps.sw-cms.detail.label.blockCategory.<category>` snippet |
| `Component.override` template has no effect | Overridden block name does not exist in the original template |
| Block setting vanishes after save | Invented property outside `customFields` |
| Twig: "Impossible to access an attribute on a null variable" | Resolver never called `setData`, or not tagged with `shopware.cms.data_resolver` |
| New config key empty on existing layouts only | `defaultConfig` runs at slot creation only — add the same default to the Twig/SCSS fallback, or re-save each layout |
| An "empty = inherit the theme value" field stops inheriting once touched, and never shows what it would inherit | `mt-number-field` coerces a cleared input to `0` (its `allowEmpty` escape hatch is deprecated), a valid explicit value that beats the token — model the key as an `mt-select` with an explicit inherit option (value `''`) |
| Select renders blank on layouts saved before the field became a select | The stored value is `null`, which matches no option — normalise `null` to `''` right after `initElementConfig` |
| Grid broken | Block template emits its own `.row` |
| Override has no effect | Path differs from original, or cache not cleared |
| Fix disappears after `composer install` | Change was made in `vendor/` |
