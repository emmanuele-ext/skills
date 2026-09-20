# Modifying an existing or core component

Pick the narrowest rung that reaches the result. Each rung up is maintenance debt paid on every Shopware/parent-theme update.

## 1. Appearance only → SCSS

Child theme compiles after the parent, so it wins at equal specificity. No `!important` — scope under the component's root class:

```scss
.acme-hero-card { .cms-element-image { border-radius: 8px; } }
```

Never use a global selector in a child theme — it can break the parent theme on an unrelated page.

## 2. Storefront markup → `sw_extends`

Create the file at the **exact same relative path** as the original (theme resolves `views` chain, last wins):

```twig
{% sw_extends '@Storefront/storefront/element/cms-element-image.html.twig' %}
{% block element_image_inner %}
    <div class="acme-image-wrapper">{{ parent() }}</div>
{% endblock %}
```

Hook the innermost block that contains the change; use `{{ parent() }}` wherever possible. Find block names with:

```bash
grep -n "{% block" vendor/shopware/storefront/Resources/views/storefront/element/cms-element-image.html.twig
```

**To suppress something the parent renders conditionally, override the block it already wraps that thing in and skip `parent()`** — never copy the parent's decision logic into your own template. The parent only reaches the block once it has decided to render, so a `{% if not <my condition> %}{{ parent() }}{% endif %}` is the whole change, and none of the parent's other reasoning is duplicated into a file that will drift on the next update.

**Never rely on a parent-theme condition you have not seen fire.** Read the whole chain of variables that feeds it, not the one line that mentions your case: a check that reads like a general rule ("the first section has a background colour") is often just a fallback *inside* a feature gate that is off by default, so satisfying it changes nothing. Confirm in the rendered DOM before designing around it.

## 3. Editor UI → `Component.override`

```js
Shopware.Component.override('sw-cms-block-layout-config', {
    template: `
        {% block sw_cms_block_config_content %}
            {% parent %}
            <mt-text-field v-if="block.type === 'acme-hero-grid' && block.customFields"
                           v-model="block.customFields.cardPadding" />
        {% endblock %}
    `,
});
```

`{% parent %}` first, always — otherwise the original block content is lost. Always guard added content with `block.type`/`element.type`, or the change applies to every layout in the shop.

## 4. Data for Twig

- Twig extension for lightweight, render-time-computable data.
- Resolver decoration for anything requiring a DB query, to keep batch loading.

## 5. Default/config of a core element → don't override it

Register a brand-new element with its own name instead. Overriding `sw-cms-el-config-image` changes the image element for the entire shop, forever, including pages nobody has in mind yet. This is the most commonly mis-chosen rung.

## 6. Site-wide, client-editable setting → theme panel

Add a field to `theme.json`; it becomes a same-named Sass variable usable across storefront SCSS. Requires `bin/console theme:refresh` + recompile.
