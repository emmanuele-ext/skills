# Why each naming principle exists

Each entry: the principle, the reason, and what breaks without it. Use this file to justify a recommendation or to judge an edge case the rules do not cover.

## Core principles (SKILL.md Hard Rules)

### Name the role, not the appearance
- **Why:** appearance changes more often than purpose. A redesign changes the color of an element, but its role stays the same.
- **Breaks without it:** `$red-price` holds green after a rebrand. The name now says something false, and every reader has to check the value before trusting it.

### Reuse the existing vocabulary
- **Why:** readers search code by the words they already know. One concept with one word makes search, grep, and conversation work.
- **Breaks without it:** `customer`, `client`, and `user` for the same entity. Each reader wonders whether these are three different things, and a search finds only a third of the usages.

### Follow the host convention
- **Why:** consistency is a form of predictability. A reader who has learned the pattern once can guess every other name.
- **Breaks without it:** mixed `camelCase`/`snake_case`, or a mix of prefixed and unprefixed names, force readers to look up each name instead of deducing it.

### No vague fillers (`data`, `manager`, `util`, ...)
- **Why:** these words fit any name, so they tell the reader nothing. They usually show that the responsibility was never clearly defined.
- **Breaks without it:** `UserManager` grows into a dumping ground because the name allows anything inside it. `data` forces the reader to trace where the value came from.

### Booleans read as yes/no questions
- **Why:** `if (isVisible)` reads like a sentence. The prefix also tells you the type without looking at it.
- **Breaks without it:** `visible` could be a boolean, a list of visible items, or a function. Negative names (`isNotHidden`) create double negations like `!isNotHidden`, which cause logic mistakes.

### Verbs for functions, nouns for values
- **Why:** a function does something and a value is something. Using the matching grammar shows at a glance whether a call has effects.
- **Breaks without it:** `order()`: does it create, fetch, or sort? Readers have to open the function to find out.

### Length scales with scope
- **Why:** the farther a name travels from where it is declared, the less context the reader has. A name must carry the context the reader no longer sees.
- **Breaks without it:** a global `i` or `x` means nothing at the point of use. A loop counter named `currentProductIndexInCartList` buries a simple loop.

### User-facing names use the user's words and state the outcome
- **Why:** users do not know your architecture. They look for what they want to achieve, and a feature they cannot recognize goes unused.
- **Breaks without it:** low adoption of a feature that works, because "Smart Sync Engine" does not tell anyone it saves their cart across devices.

### Repo and agent instructions win for commits
- **Why:** commit format is a team contract that tools and people already rely on (changelogs, release scripts, review habits). A generic skill cannot know it.
- **Breaks without it:** the skill overrides the team format, which breaks history tooling and forces rewrites.

### Never rename a public name without listing its consumers
- **Why:** a name is an interface. Other code, templates, stored data, and other teams depend on the exact spelling.
- **Breaks without it:** silent failures. CSS falls back to defaults, a template prints an empty value, or a migration misses a column. None of these produce an error you see right away.

## Process (SKILL.md Execution Steps)

### Search before inventing
- **Why:** the best name is often one the project already uses. Finding it costs a search, while missing it creates a synonym that lives forever.

### One recommendation, alternatives only with a real tradeoff
- **Why:** a list of equal options moves the decision back to the person who asked. Alternatives are useful only when they map to a different situation.

### The new-teammate test
- **Why:** the author always understands their own name, so their opinion is not a useful check. Someone without context is the person a name has to work for.

## Category rules (references/categories.md)

### code
- **Units in the name (`timeoutMs`):** most bugs with numbers come from mixing units. The name is the only place the unit is visible at the point of use.
- **`get` vs `fetch`/`load`:** the verb signals cost. Readers expect `get` to be cheap and synchronous, so a `get` that does a network call gets used inside loops.
- **Singular class name of the responsibility:** a class models one concept. If the name cannot state the responsibility, the class probably has more than one.

### css
- **Stick to the project's methodology:** two methodologies in one codebase double what a reader must know to predict selectors.
- **Modifiers describe state or variant, not color:** the same reason as role over appearance. `--discount` survives a palette change, `--red` does not.
- **Separate JS hooks (`js-`, `data-*`):** a styling rename must not break behavior. Separate hooks make it clear which classes are safe to change.

### tokens
- **Layers (primitive → semantic → component):** each layer changes for a different reason. The palette changes during a rebrand, the semantic mapping changes for themes or dark mode, and component tokens change when one component is adjusted. Layers let you change one level without touching the others.
- **Fixed segment order:** with a predictable order, a token name can be built from memory and autocompletion groups related tokens together.
- **One size scale:** mixing `md` and `16` means nobody knows which is bigger, and spacing drifts.

### components
- **Industry-standard names:** a new developer already knows what `Drawer` or `Toast` means. A custom word has to be learned, and people searching for help online will not find it.
- **Name by function, not page:** components get reused. `HomepageBox` sounds wrong on the product page, so someone duplicates it instead.
- **Variants as props:** one component per variant multiplies code that has to stay in sync. A prop keeps a single source.

### features
- **Outcome over mechanism:** see "User-facing names" above. Users adopt what they understand.
- **Keep codenames out of the UI:** a codename makes sense only inside the team. It also locks the product to an internal joke that is hard to remove later.

### git
- **Branch names describe the change:** while many branches exist at once, the branch name is the only label in lists and PRs. A ticket number or a person's name alone says nothing about the change.
- **Imperative subject:** the subject completes the sentence "applying this commit will ...". It also matches the messages Git generates, such as `Merge branch`.
- **One logical change per commit:** revert, cherry-pick, and bisect work per commit. Mixing changes means you cannot undo one without the other.
