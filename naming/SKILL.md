---
name: naming
description: "Trigger: naming or renaming anything: variable, function, class, CSS class, design token, color, component, feature, file, branch, commit, product. Pick clear names that state role, not looks."
license: Apache-2.0
metadata:
  author: emmanuele
  version: "1.0"
---

## Activation Contract

Load when choosing, reviewing, or renaming any identifier or label: code symbols, CSS classes, design tokens, UI components, features, files, branches, commit subjects, products. Also load when the user asks "how should I call X".

## Hard Rules

- Name the **role or purpose**, never the appearance or implementation: `color-text-muted`, not `grey-500-text`; `is-active`, not `blue-border`.
- Reuse the codebase's existing vocabulary first. Grep for the concept before inventing a word; one concept = one word across the project.
- Follow the host convention (case, prefixes, order) already in use. Never mix conventions in one scope.
- No vague fillers: `data`, `info`, `manager`, `helper`, `util`, `handle`, `stuff`, `new`, `temp`, `2`, `final`.
- Booleans read as yes/no questions: `is`, `has`, `can`, `should`.
- Functions start with a verb; values and types are nouns.
- Length scales with scope: short in a 3-line loop, fully descriptive when exported or global.
- User-facing names (features, CMS blocks, products) use the user's words and state the outcome, not the mechanism.
- Commit messages: the global commit rules in CLAUDE.md and the project's AGENTS.md win over this skill; apply only the clarity rules here.
- Never rename public APIs, tokens, or DB fields without flagging every consumer that breaks.

## Decision Gates

| Naming target | Apply |
|---------------|-------|
| Variable, function, class, file | `references/categories.md#code` |
| CSS class, SCSS variable | `references/categories.md#css` |
| Design token, color | `references/categories.md#tokens` |
| UI component | `references/categories.md#components` |
| Feature, CMS block, product | `references/categories.md#features` |
| Git branch, commit subject | `references/categories.md#git` |
| Two terms in the code mean the same thing | Pick the one most used, list the others as rename candidates |

## Execution Steps

1. Identify the target category and its scope (local, module, public, user-facing).
2. Search the codebase for existing names of the same concept and sibling names of the same kind.
3. Apply the category rules from the reference.
4. Produce 1 recommended name; add up to 2 alternatives only if they carry a real tradeoff.
5. Check: would a new teammate guess what it is from the name alone? If not, revise.

## Output Contract

Return the recommended name first, then one line of reasoning. Alternatives, if any, as `name — tradeoff`. For renames, list affected files/consumers.

## References

- `references/categories.md` — per-category rules, patterns, and good/bad examples.
