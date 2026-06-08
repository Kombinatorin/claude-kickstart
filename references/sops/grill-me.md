# SOP: Grill-Me Skill

## Was es ist

Interview-Pattern, das nach **jeder** Antwort auf Disk persistiert. Default: Claude stellt eine Frage, gibt einen **Recommended Answer** (best guess aus Kontext), du bestätigst, korrigierst oder redirektest. Bei „weiß ich nicht" → Flag mit Owner, weiter zur nächsten Frage. Kein Blockieren, kein Vergessen.

Skill-Datei: `.claude/skills/grill-me/SKILL.md`

## Wann nutzen (≥2 Signale)

- Sprache: „lass uns überlegen", „ich weiß nicht genau", „mehrere Optionen", „was denkst du"
- Thema berührt ≥2 `context/*.md` Bereiche (z.B. work + goals + team)
- Neues Modul, Pilot oder Projekt-Aufsetzen
- Strategischer Pivot
- ≥3 offene Entscheidungen in einem einzigen Prompt

## Wann NICHT nutzen

- Konkrete Coding-Tasks
- Single-Action-Requests („deploy", „commit", „lies File X")
- Recherche / Lookups
- Mini-Fragen mit klarer Antwort

## Aufruf

```
/grill-me <topic-stichwort>
```

Beispiele:

- `/grill-me Tech-Stack-Wahl-MVP`
- `/grill-me Naming-für-neues-Produkt`
- `/grill-me Q3-Roadmap-Priorisierung`

## Capture-Ort

Fester Default: `operations/brainstorms/{YYYY-MM-DD}-{topic-slug}.md`

Eine vorhersagbare Adresse. Nicht in Projekt-Ordner streuen. Brainstorm-File bleibt **immer** hier — als Roh-Audit, auch wenn das Ergebnis ins Projekt wandert.

## Nach der Session — Promotion-Pfade

| Was | Ziel | Template |
|-----|------|----------|
| Entscheidungen | `decisions/log.md` | `templates/decision-entry.md` |
| Konkrete Tasks / To-Dos | `operations/inbox.md` | — |
| Neues Vorhaben | `projects/active/<x>/STRATEGY.md` | `templates/project-readme.md` |

Brainstorm-File **nie löschen, nie verschieben**.

## Disziplin (für Claude)

- Vor erster Frage: Datei anlegen, kurz mitteilen wo
- Nach **jeder** Antwort: append, **dann** nächste Frage
- Bei „weiß ich nicht" → Flag + Owner, weiter
- Am Ende: Reconciliation-Lesedurchgang + Recap + Promotion-Vorschläge

## Beispiel-Trigger (mental model)

> User: „/grill-me Q3-Roadmap-Priorisierung"
> Claude: liest `context/work.md` (Produkt-/Themen-Kontext) + `context/goals.md` (Quartalsziele), legt `operations/brainstorms/2026-06-08-q3-roadmap-priorisierung.md` an, stellt erste Frage mit Recommendation aus dem Kontext, persistiert Antwort, weiter.

## Friction-Review

Nach 2–4 echten Sessions im Alltag: passt der Ort? Stört der Recommended-Answer-Stil? Anpassungen direkt in SOP und SKILL.md fließen lassen — siehe `.claude/rules/update-feedback-loops.md`.
