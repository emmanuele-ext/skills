# Naming rules by category

## code

- Variables: noun of what it holds, plural for collections (`orders`, `orderById`).
- Booleans: `isVisible`, `hasStock`, `canCheckout`. Avoid negatives (`isNotHidden`).
- Functions: verb + object (`fetchOrders`, `calculateShipping`). `get` = cheap/sync, `fetch`/`load` = I/O, `build`/`create` = new instance.
- Classes: singular noun of the responsibility (`PriceCalculator`, not `PriceManager`).
- Units in the name when not typed: `timeoutMs`, `widthPx`, `priceCents`.
- Files: match the main export; follow the folder's existing case.
- Bad → good: `data` → `cartItems`; `handle()` → `submitOrder()`; `flag` → `isGuestCheckout`.

## css

- Follow the project methodology (BEM, utility, Shopware/Bootstrap). Do not introduce a new one.
- BEM: `block__element--modifier`; block = component name, modifier = state or variant (`--active`, `--compact`), not color.
- States: `is-open`, `has-error`.
- JS hooks separate from styling: `js-` prefix or `data-*` attributes.
- SCSS variables follow the token rules below.
- Bad → good: `.red-text` → `.price--discount`; `.box2` → `.product-card__badge`.

## tokens

- Layered structure, each layer referencing the previous:
  1. Primitive: `color-green-600`, `space-4` (describe the value, never used directly in components).
  2. Semantic: `color-text-primary`, `color-surface-muted`, `space-inset-md` (describe the role).
  3. Component: `button-primary-background-hover` (describe usage in one component).
- Order: `category-property-variant-state` (e.g. `color-background-primary-hover`). Keep the same order everywhere.
- Size scales: pick one (`xs/sm/md/lg/xl` or numeric) and never mix.
- Primitive color names: hue + step (`green-600`). Use a descriptive name ("sage") only for brand palettes, and map it to a semantic token.

## components

- Use the name most design systems use (button, card, badge, tag, chip, accordion, tabs, modal/dialog, drawer, toast, tooltip, popover, breadcrumb).
- Name by function, not by page: `ProductTeaser`, not `HomepageBox`.
- Variants are props/modifiers, not new components: `Button variant="ghost"`, not `GhostButton`, unless the behavior differs.
- Container vs presentational: `OrderListContainer` / `OrderList`.

## features

- State the outcome for the user (job-to-be-done): "Riordina in un clic", not "Quick Order Engine".
- Use words users already say; if unsure, ask how they would describe it and adopt that.
- Avoid internal codenames in UI. Keep them only in code/tickets, and map them explicitly.
- CMS blocks/elements: the merchant searches the picker by content type ("Galleria con testo"), so name by what it shows.
- Products: short, pronounceable, unique in the domain. Check for trademark conflicts before adopting.

## git

- Branch: `<type>/<short-kebab-description>` following the repo's flow (`feature/`, `fix/`, `hotfix/`, `release/`). Describe the change, not the person or ticket alone (`feature/filtro-categoria`; add the ticket ID if the team uses one).
- Commit subject: the format comes from the agent's instruction files and the repo's conventions (prefix, emoji, type, language, length). This skill adds only:
  - Imperative, describes the effect: `fix: allinea prezzo barrato al prezzo` beats `fix: css`.
  - Understandable without reading the diff.
  - One logical change per subject; if it needs "and", split the commit.
- Tags/releases: follow the repo's versioning (e.g. `X.Y.Z`).
