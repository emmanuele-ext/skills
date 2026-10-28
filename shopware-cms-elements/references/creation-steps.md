# Creating a CMS element from zero

## 1. Decide it's actually needed

- Does a core element already cover it? Use that.
- Is the requirement "new arrangement" not "new content"? Write a block, not an element.
- Will config hold entity ids? A resolver will be needed too.

## 2. Pick the name

Format `<vendor>-<description>`, lowercase, hyphenated, e.g. `acme-hero-card`. It derives:

```
acme-hero-card                          → registerCmsElement({ name })          (bound)
cms-element-acme-hero-card.html.twig    → storefront template                   (bound)
'acme-hero-card'                        → resolver getType()                    (bound)
sw-cms-el-acme-hero-card                → canvas component                      (convention)
sw-cms-el-config-acme-hero-card         → config component                      (convention)
sw-cms-el-preview-acme-hero-card        → preview component                     (convention)
```

## 3. Folder skeleton

```
Resources/app/administration/src/module/sw-cms/elements/acme-hero-card/
├── index.js
├── config.constant.js
├── component/{index.js, sw-cms-el-acme-hero-card.html.twig}
├── config/{index.js, sw-cms-el-config-acme-hero-card.html.twig}
└── preview/{index.js, sw-cms-el-preview-acme-hero-card.html.twig}

Resources/views/storefront/element/cms-element-acme-hero-card.html.twig
Resources/app/storefront/src/scss/component/_acme-hero-card.scss
```

## 4. `config.constant.js`

Declare every field as `{ source: 'static', value }`; add `entity: { name }` when the value is an id:

```js
export const ACME_HERO_CARD_DEFAULT_CONFIG = {
    media:       { source: 'static', value: null, required: true, entity: { name: 'media' } },
    headline:    { source: 'static', value: 'Titolo' },
    buttonLabel: { source: 'static', value: 'Scopri' },
    buttonUrl:   { source: 'static', value: '' },
    variant:     { source: 'static', value: 'light' },
};
```

## 5. Three Vue components

- **preview**: static markup only, no logic, no data.
- **config**: `mixins: [Shopware.Mixin.getByName('cms-element')]`, `emits: ['element-update']`, `initElementConfig()` in `created()`, one `mt-*` field per config entry, each with `@update:model-value="emitUpdate"`, and an own `emitUpdate()` method that emits `element-update` (the mixin does not provide it).
- **component**: same mixin and `initElementConfig` call, markup that resembles the storefront output — never business logic, never a second implementation to keep in sync.

## 6. `index.js` + registration

```js
import { ACME_HERO_CARD_DEFAULT_CONFIG } from './config.constant';
import './component';
import './config';
import './preview';

Shopware.Service('cmsService').registerCmsElement({
    name: 'acme-hero-card',
    label: 'acme.cms.elements.heroCard.label',
    component: 'sw-cms-el-acme-hero-card',
    configComponent: 'sw-cms-el-config-acme-hero-card',
    previewComponent: 'sw-cms-el-preview-acme-hero-card', // mandatory
    defaultConfig: ACME_HERO_CARD_DEFAULT_CONFIG,
});
```

Then import it from `Resources/app/administration/src/main.js` — without this import nothing above ever runs.

## 7. Storefront template

`Resources/views/storefront/element/cms-element-acme-hero-card.html.twig`. Read `element.translated.config`, entities from `element.data`, wrap each part in a named `{% block %}`, guard optional `<a>`/`<img>` output, use `sw_thumbnails`.

## 8. Resolver (only if config holds entity ids)

Implement `AbstractCmsElementResolver`: `getType()` returns the exact element name; `collect()` returns a `?CriteriaCollection` (batched across the whole page, don't query manually); `enrich()` always calls `$slot->setData($struct)` before any return, even with an empty struct. Register with tag `shopware.cms.data_resolver` in `services.xml` (or `services.php`) — untagged resolvers are silently never called.

## 9. Translations

Add every key used in `label` and `$t(...)` to the administration snippet files — untranslated keys render as the raw key string.

## 10. Build and verify

```bash
bin/build-administration.sh
bin/console cache:clear
```

Then run the checklist in `troubleshooting.md`.

## Block variant

Blocks follow the same naming discipline via `registerCmsBlock`, but:

- `category` is free-form. The built-in ones are `text`, `image`, `video`, `text-image`, `commerce`, `sidebar`, `form`, `html` (plus `app`). Any other value creates a new tab in the block sidebar, labelled by the snippet key `apps.sw-cms.detail.label.blockCategory.<category>` — add that snippet, or the tab shows the raw key. Prefer a built-in category unless the blocks genuinely need their own tab.
- `slots` declares the element `type` occupying each slot, optionally with `default: { config, data }` using `source: 'default'` + `Shopware.Constants.CMS.MEDIA.*` placeholders.
- The storefront block template emits only `col-*` wrappers (never its own `.row`) and includes the slot's element dynamically: `{% sw_include '@Storefront/storefront/element/cms-element-' ~ element.type ~ '.html.twig' ignore missing %}`. Always add `data-cms-element-id="{{ element.id }}"`.
- Any block setting outside the fixed DB columns must go in `customFields` (see SKILL.md Hard Rules), never invented as a top-level `defaultConfig` key.
