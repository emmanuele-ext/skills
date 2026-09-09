---
name: shopware-cms-elements
description: "Trigger: Shopware CMS element, CMS block, registerCmsElement, registerCmsBlock, sw-cms-el, cms-element twig, Shopware resolver, sw_extends, Shopping Experiences. Write and modify custom Shopware 6 CMS elements/blocks correctly on the first try."
license: Apache-2.0
metadata:
  author: emmanuele
  version: "1.0"
---

## Activation Contract

Activate when writing, registering, or debugging a Shopware 6 CMS element or block (administration Vue component, storefront Twig template, PHP resolver), or when extending an existing core/theme CMS component.

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
