---
name: shopware-cms-elements
description: "Trigger: any Shopware 6 storefront or administration customization. CMS element or block (registerCmsElement, registerCmsBlock, sw-cms-el, cms-element twig, Shopping Experiences, cms data resolver); child-theme SCSS override; Twig override via sw_extends; Shopware Component.override; theme.json setting; custom plugin under custom/plugins/; or the rebuild after one (build-administration, build-storefront, theme:compile, cache:clear). Pick the narrowest override rung, never touch vendor/, and catch the silent failures on the first try."
license: Apache-2.0
metadata:
  author: emmanuele
  version: "1.1"
---

## Activation Contract

Activate on any Shopware 6 work whose output is storefront markup, storefront styling, or administration UI:

- writing, registering, or debugging a CMS element or block (administration Vue component, storefront Twig template, PHP data resolver);
- changing an existing core or parent-theme component from a child theme or plugin — SCSS, `sw_extends` Twig override, `Shopware.Component.override`, `theme.json`;
- choosing which build or cache command a change needs, or diagnosing a change that appeared to do nothing.

Do not activate for Shopware work that produces no storefront or administration output: Composer and plugin lifecycle on their own, database or migration work, API, import, or integration code.

## Hard Rules

- Everything custom lives under `custom/plugins/`. Never edit `vendor/` — Composer overwrites it silently.
- A CMS element/block is bound together only by its **name string**, in exactly these places, and they must match verbatim:
  `registerCmsElement({name})` / `sw-cms-el-<name>`, `sw-cms-el-config-<name>`, `sw-cms-el-preview-<name>` / `cms-element-<name>.html.twig` / `getType()` in the resolver.
  A mismatch produces no error — only silent failure (missing from picker, blank render). See `references/troubleshooting.md`.
- Choose a name once, `<vendor>-<description>`, lowercase, hyphenated. Never rename after creation.
- `previewComponent` is mandatory on `registerCmsElement` — without it the element never appears in the picker, no error.
- `initElementConfig('<name>')` must run in `created()` in **both** the config and component Vue files (two separate instances). Keep `defaultConfig` in one `config.constant.js` imported by both — never duplicate it.
- Every field mutation must `$emit('element-update', this.element)`, wired via `@update:model-value="emitUpdate"` on each field — otherwise the save button never activates.
- Storefront templates read `element.translated.config` (never `element.config`) and loaded entities from `element.data`. Wrap every `{% block %}` around a logically overridable chunk. Never emit `<a href="">` or `<img src="">` for empty optional fields. Use `sw_thumbnails`, never raw `<img>`.
- **Blocks have a fixed DB column set** (`marginTop/Bottom/Left/Right`, `cssClass`, `backgroundColor`, `backgroundMediaId`, `sizingMode`) plus `customFields` (JSON). Any other invented key in a block's `defaultConfig` is silently discarded on save. Elements have no such limit.
- If a setting concerns a single slot, it belongs on the element's config, never on the block.
- Block storefront templates emit only `col-*` (no own `.row`) and resolve the included element template dynamically from `element.type` via `sw_include ... ignore missing`, never hardcoded.
- A resolver is needed only if the config holds an **entity id**. It must call `slot->setData($struct)` unconditionally before any early return, and be registered with the `shopware.cms.data_resolver` tag — otherwise it is silently never called.

## Decision Gates

| Need | Do |
|---|---|
| New layout of existing content | Write a **block** |
| New configurable content type | Write an **element** |
| Config value is a raw id (media/product/category) | Add `entity: { name }` and write a resolver |
| Modifying an existing/core component | Use `references/override-ladder.md` — pick the narrowest rung, never edit `vendor/` |
| Creating an element from scratch | Follow `references/creation-steps.md` in order |
| Something silently doesn't work | Check `references/troubleshooting.md` first |

## Output Contract

- Naming consistent across all 5 derived artifacts (see Hard Rules).
- Vue element ships three components (component/config/preview) + `config.constant.js`; Twig storefront template mirrors the config structure.
- End every new element/block with the 5-point verification checklist in `references/troubleshooting.md`.

## References

- `references/creation-steps.md` — full folder skeleton and step-by-step build order for a new element.
- `references/override-ladder.md` — six-rung escalation for modifying existing/core components (SCSS → new element).
- `references/troubleshooting.md` — build commands per file type, common silent-failure symptoms and causes, verification checklist.
