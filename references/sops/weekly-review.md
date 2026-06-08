# SOP: Weekly Review

## Was es ist

Strukturierter Wochenrückblick + Ausblick. Liest alle relevanten Dateien, fragt 5 Fragen, schreibt einen Eintrag in `operations/weekly-review.md` und aktualisiert `context/current-priorities.md`.

Command-Datei: `.claude/commands/weekly-review.md`

## Wann nutzen

- **Freitagnachmittag** (idealer Cliffhanger fürs Wochenende)
- **Sonntagabend** (Setup für die kommende Woche)
- Nie unter Stress oder zwischen Tür und Angel — der Wert kommt aus ehrlichen, ruhigen Antworten

## Wann NICHT nutzen

- Mitten in einer Coding-Session
- Wenn die Woche noch nicht beendet ist (Dienstag-Mittagsversuch = falscher Zeitpunkt)
- Wenn du dich gerade über etwas ärgerst — emotionale Färbung verfälscht den Review

## Die 5 Fragen

1. **"Was hast du diese Woche erreicht — auch kleine Sachen?"**
2. **"Was hast du nicht geschafft? Was war der echte Grund (nicht der bequeme)?"**
3. **"Welche 1–3 Lessons Learned willst du nächste Woche umsetzen?"**
4. **"Wie war deine Energie diese Woche auf 1–5? In einem Satz: warum?"**
5. **"Was sind die 1–3 wichtigsten Outcomes für nächste Woche?"**

Wichtig: Eine Frage nach der anderen. Wenn der Nutzer ausweicht, einmal kurz nachhaken — dann weiter.

## Output-Format

Neuer Eintrag **oben** in `operations/weekly-review.md`:

```
## Woche YYYY-Wxx (Mo TT.MM – So TT.MM)

### Was erreicht
- ...

### Was nicht erreicht (und warum)
- ... — Grund

### Lessons Learned
- ...

### Fokus nächste Woche
- ...

### Energie / Stimmung (1–5)
[Zahl] — [1 Satz]
```

Plus: `context/current-priorities.md` mit den Outcomes für nächste Woche aktualisieren.

## Zusätzlich

Wenn beim Review eine größere Entscheidung getroffen wurde (Pivot, Projekt einstampfen, Tool wechseln), nach dem Review-Eintrag noch:

- Eintrag in `decisions/log.md` mit `templates/decision-entry.md`-Format
- Ggf. Projekt-Status in `projects/active/<x>/status.md` aktualisieren

## Friction-Review

Wenn `weekly-review.md` nach 4 Wochen leer ist: Honest Check, ob Freitag/Sonntag der richtige Tag ist — oder ob die Fragen umformuliert werden müssen, damit sie nicht wie Pflicht wirken.
