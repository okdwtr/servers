# AGENTS Instructions

## Scope
This file applies to the entire repository.

## Workflow
- Make small, focused commits (roughly one commit per logical change).
- Before making changes, check for nested `AGENTS.md` files in the target directories.

## Style
- Keep configuration files maintainable with concise comments.
- For infrastructure definitions, prefer explicit defaults over implicit behavior.

- Traefik compose and Traefik configuration should be split into separate files for readability.

- Infrastructure onboarding and routing instructions should be documented under `docs/` in Markdown.
