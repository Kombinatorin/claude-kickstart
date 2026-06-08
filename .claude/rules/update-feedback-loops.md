# Update Feedback Loops

## Goal
Keep the system stable as tools, APIs, MCP integrations, and model behavior evolve.

## Failure Analysis
When something breaks, classify the issue:
- outdated platform assumption
- outdated reference
- tool/API change
- bad input contract
- unclear handoff
- implementation defect

## Response
- fix the smallest stable layer first
- prefer updating references, rules, or contracts over rewriting everything
- document repeated failure patterns
- improve the relevant file at the right layer

## Stability Standard
- agent roles should remain stable
- skills should use explicit inputs and outputs
- references should absorb external change
- global control files should stay lean
