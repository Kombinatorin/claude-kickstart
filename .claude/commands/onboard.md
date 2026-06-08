# Onboard

Geführter Erst-Aufbau aller `context/*.md`-Dateien. Pro Datei eine Phase, eine Frage nach der anderen, sofortige Persistenz.

Dieser Command wird typischerweise einmal pro Repo aufgerufen — direkt nach dem Klonen. Er ist die geführte Variante von `/grill-me` mit fixem Curriculum.

---

## Phase 0 — Willkommen & System-Erklärung

Gib genau diese drei Absätze aus (keine Variation):

```
👋 Willkommen bei Claude-Kickstart.

Das hier ist ein modulares Operating-Model für Claude Code — Regeln, Agents,
Skills, Templates und Rituale, die du sofort nutzen kannst. Du musst keine
Datei selbst anlegen, kein Verzeichnis erfinden. Was du nicht magst, änderst
oder löschst du später einfach.

Ich führe dich jetzt durch dein persönliches Setup: 7 kurze Phasen, in jeder
fülle ich eine Datei in context/ mit dir gemeinsam. Pro Phase 3–6 Fragen,
eine nach der anderen. Bei "weiß nicht" markiere ich die Stelle und gehe
weiter — du kannst alles später ergänzen.

WICHTIG: Alles was du gleich siehst, ist ein Vorschlag. Du darfst Struktur,
Felder, sogar ganze Dateien ändern. Die Templates sind dein Startpunkt,
nicht dein Käfig.
```

Frage dann: **"Bereit? (ja / kurz pausieren / Beispiel ansehen)"**

- "ja" → Phase 1
- "kurz pausieren" → kurz erklären dass `/onboard` jederzeit wieder aufgerufen werden kann
- "Beispiel ansehen" → den Inhalt von `references/examples/filled-context/me.md` zeigen, dann wieder fragen ob bereit

---

## Phasen 1–7 — Geführtes Befüllen

Reihenfolge (fest):

1. `context/me.md` — Identity
2. `context/preferences.md` — Arbeits-/Kommunikationsstil
3. `context/work.md` — Beruflicher Kontext
4. `context/goals.md` — Ziele
5. `context/tools-stack.md` — Werkzeuge
6. `context/team.md` — Team oder Solo
7. `context/current-priorities.md` — Aktueller Fokus

### Pro Phase — strikt dieser Ablauf

1. **Ankündigen** in einem Satz: "Phase X von 7 — wir füllen jetzt `context/<datei>.md`. (Thema in 1 Satz)"
2. **Datei lesen** um die Sektionen zu sehen (sie sind die Frage-Vorlage)
3. **Sektion für Sektion durchgehen** — pro Sektion: eine Frage stellen, kurz die Erwartung skizzieren ("kurze Stichpunkte reichen / 2–3 Sätze")
4. **Nach jeder Antwort** sofort die Datei updaten (Sektion ersetzen, HTML-Kommentar-Header behalten)
5. **Bei "weiß nicht"** Sektion mit `<!-- TBD -->` markieren, weitermachen
6. **Nach allen Sektionen der Phase** kurzen Recap geben:
   ```
   ✅ context/<datei>.md ist befüllt. Schau gern kurz rein — Pfad: context/<datei>.md.
   Bereit für Phase X+1? (ja / kurz reinschauen)
   ```

**Wichtig zur Disziplin:**

- Niemals mehrere Fragen auf einmal
- Niemals Antworten "für später" sammeln — immer sofort in die Datei schreiben
- Niemals erfundene Inhalte hinzufügen — nur das was der Nutzer wirklich gesagt hat
- HTML-Coaching-Kommentare am Dateikopf bleiben unangetastet

---

## Phase 8 — Skills- & Shortcuts-Tour

Nach dem Befüllen erklärst du die wichtigsten Skills/Commands in **kompakten Karten** — eine nach der anderen, jeweils mit Pause "weiter?":

````
## /grill-me <thema>
**Was es macht:** Befragt dich relentless zu einem Thema, schreibt jede Antwort
sofort in eine Capture-Datei in operations/brainstorms/.
**Wann nutzen:** Wenn du Klarheit über eine Entscheidung, ein Konzept oder einen
Plan brauchst und der Kopf voll ist.
**Einfluss:** Nichts wird "produktiv" — alles bleibt im Brainstorm-File, bis du
es bewusst in decisions/log.md oder einen Projekt-Ordner promotest.
````

````
## /session-start
**Was es macht:** Liest context/*.md und operations/inbox.md, gibt dir einen Tagesplan.
**Wann nutzen:** Immer am Anfang einer Arbeits-Session.
**Einfluss:** Nur Lese-Operationen, kein Schreiben (außer am Montag — dann wird
der Wochenplan in operations/weekly-review.md ergänzt).
````

````
## /session-end
**Was es macht:** Fragt was passiert ist, schreibt eine Session-Summary,
aktualisiert operations/inbox.md mit offenen Punkten.
**Wann nutzen:** Am Ende einer Session — auch kurze.
**Einfluss:** Schreibt in operations/, niemals in context/ ohne Rückfrage.
````

````
## /weekly-review
**Was es macht:** Wochenrückblick + Ausblick. Liest alle Session-Summaries der
Woche, fasst zusammen, fragt nach Lessons Learned.
**Wann nutzen:** Freitags oder Sonntags.
**Einfluss:** Schreibt in operations/weekly-review.md und aktualisiert
context/current-priorities.md.
````

Abschluss-Frage: **"Möchtest du jetzt dein erstes Projekt anlegen (ich rufe project-bootstrapper auf), oder lieber selbst durchs Repo navigieren?"**

---

## Phase 9 — Wrap-up & Anpassungs-Hinweis

Gib genau diesen Block aus:

```
🎉 Setup abgeschlossen.

Dein Kontext liegt in context/*.md — 7 Dateien, die ich bei jeder Interaktion
mit dir lese. Du kannst sie jederzeit direkt im Editor ändern.

Ein paar Hinweise:

- Beispiele zum Vergleich: references/examples/filled-context/
  Eine fiktive Persona "Sam, Indie-Developer" — guck rein, ob dein eigener
  Kontext mindestens so konkret ist.

- SOPs zu allen Ritualen: references/sops/
  Wenn dich ein Skill oder Command stört oder verwirrt, schau in die SOP
  und ändere sie.

- Update-Schleife: .claude/rules/update-feedback-loops.md
  Wenn dieses Repo an irgendeiner Stelle reibt, ändere die reibende Stelle.
  Nichts hier ist heilig.

Das Repo gehört dir.
```

---

## Wenn der Nutzer mittendrin abbricht

Akzeptieren ohne Drama. Sagen:

```
Okay — die schon befüllten Dateien bleiben erhalten. Du kannst /onboard
jederzeit wieder aufrufen, um an der letzten unvollständigen Phase
weiterzumachen. Bisher gefüllt: context/<datei1>.md, context/<datei2>.md …
```

Beim nächsten `/onboard`-Aufruf: prüfen welche `context/*.md` schon echten Inhalt haben (keine `<!-- TBD -->` und keine leeren Sektionen) und mit der ersten unvollständigen Phase starten.

$ARGUMENTS
