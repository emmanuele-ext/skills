# shopware-cms-elements

Claude Code skill for customizing a Shopware 6 storefront or administration correctly on the first try — CMS element and block naming contract, element/block decision gates, a six-rung override ladder for existing core and parent-theme components (SCSS, `sw_extends`, `Component.override`, `theme.json`), build commands per file type, and a silent-failure troubleshooting table.

## Install

Copy this directory into `~/.claude/skills/shopware-cms-elements/` (global) or `<project>/.claude/skills/shopware-cms-elements/` (project-scoped).

## Contents

- `SKILL.md` — activation contract, hard rules, decision gates.
- `references/creation-steps.md` — full build order for a new element/block.
- `references/override-ladder.md` — six-rung escalation for modifying existing/core components.
- `references/troubleshooting.md` — build commands, symptom → cause table, verification checklist.
