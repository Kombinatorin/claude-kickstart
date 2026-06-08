# File Governance

## Core Rule
Every file must have one clear purpose.

## Limits
- `CLAUDE.md` should stay under 150 lines whenever possible.
- Rule files should stay focused on a single topic.
- Agent files should define a role, not become general knowledge dumps.
- Long-form detail belongs in `references/`, not in control files.

## Source of Truth
- Global project rules belong in `.claude/rules/`
- Project roles belong in `.claude/agents/`
- Stable context belongs in `context/`
- Reusable references belong in `references/`
- Active work belongs in `projects/active/`
- Templates belong in `templates/`

## Anti-Bloat
- Do not duplicate the same rule in multiple places.
- Do not bury critical instructions in unrelated files.
- Do not store secrets or sensitive credentials in markdown.
