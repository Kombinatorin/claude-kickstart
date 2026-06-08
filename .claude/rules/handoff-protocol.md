# Handoff Protocol

## When to Use

Use the multi-agent workflow when a task requires design decisions before implementation,
or when independent validation is important (new features, schema changes, risky refactors).
Skip it for trivial fixes that fit in a single edit.

## Standard Flow

```
creative-strategist  →  STRATEGY.md
      ↓
  developer          →  implementation + IMPL-NOTES.md
      ↓
   tester            →  VALIDATION.md (PASS / FAIL / PARTIAL)
      ↓
 orchestrator        →  consolidated report to user
```

## File Locations

All handoff files go in the active project folder (e.g. `projects/active/my-project/`):

| File | Written by | Read by |
|------|-----------|---------|
| `STRATEGY.md` | creative-strategist | developer, tester |
| `IMPL-NOTES.md` | developer | tester, orchestrator |
| `VALIDATION.md` | tester | orchestrator, developer |

## Orchestrator Responsibilities (Claude)

1. Spawn creative-strategist with the task brief
2. Read STRATEGY.md — confirm it is coherent before spawning developer
3. Spawn developer with path to STRATEGY.md
4. Read IMPL-NOTES.md — note any deviations
5. Spawn tester with paths to STRATEGY.md and IMPL-NOTES.md
6. Read VALIDATION.md
7. If PASS: report to user, offer deploy
8. If FAIL: spawn developer again with VALIDATION.md findings, repeat from step 4
9. Send user a consolidated summary covering all three outputs

## Consolidated Report Format

```
## Ergebnis: [Feature Name]

**Status:** PASS / FAIL

**Was gebaut wurde**
[Aus IMPL-NOTES.md — 2–3 Sätze]

**Validierung**
[Aus VALIDATION.md — Kriterien-Checkboxen + Findings]

**Nächster Schritt**
[Deploy / Nachbesserung / offene Punkte]
```

## Cleanup

After a successful deploy, archive STRATEGY.md, IMPL-NOTES.md, VALIDATION.md
into a dated subfolder (e.g. `projects/active/my-project/.handoffs/2026-05-10/`)
to keep the project root clean.
