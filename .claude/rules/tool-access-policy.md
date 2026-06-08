# Tool Access Policy

## Principle
Tools are available by role and purpose, not by default convenience.

## Role Guidance
- Creative Strategist:
  - primarily read, inspect, compare, evaluate
  - should not trigger risky production actions by default
- Developer:
  - may implement, configure, and modify within approved scope
- Tester:
  - may validate, inspect logs, review outputs, and report defects
- Bootstrapper:
  - may create structure and starter files
  - should not invent large content bodies unnecessarily

## Security
- Secrets live in environment files or secure runtime configuration
- Never copy secrets into markdown
- Prefer MCP-backed access over ad hoc undocumented access

## Control
- Use references and tool notes before using unfamiliar tools
- Distinguish read access from write access
- Distinguish staging/test use from production use
