# SOP: Onboard

## Was es ist

Geführter Erst-Aufbau aller `context/*.md`-Dateien. Eine Phase pro Datei, eine Frage nach der anderen, sofortige Persistenz.

Same Engine wie `/grill-me`, aber **fixes Curriculum** statt Free-Topic.

Command-Datei: `.claude/commands/onboard.md`

## Wann nutzen

- **Direkt nach `git clone`** als erster Schritt im neuen Repo
- Nach einer großen Lebens-/Berufs-Veränderung, wenn der Kontext substanziell veraltet ist
- Wenn ein Teammitglied das gleiche Setup für sich aufbauen will

## Wann NICHT nutzen

- Wenn `context/*.md` schon befüllt sind und nur Detail-Updates anstehen — dann besser direkt im Editor oder via `/grill-me <dimension-vertiefen>`

## Curriculum

7 Phasen, fest in dieser Reihenfolge:

1. `me.md` — Identity
2. `preferences.md` — Arbeitsstil
3. `work.md` — Beruflicher Kontext
4. `goals.md` — Ziele
5. `tools-stack.md` — Werkzeuge
6. `team.md` — Team oder Solo
7. `current-priorities.md` — Aktueller Fokus

Plus:
- **Phase 0:** Willkommen & "alles ist anpassbar"-Hinweis
- **Phase 8:** Skill-Tour (`/grill-me`, `/session-start`, `/session-end`, `/weekly-review`)
- **Phase 9:** Wrap-up mit Verweis auf Beispiele + Update-Schleife

## Disziplin

Identisch zum `/grill-me`-Pattern:

- Eine Frage zur Zeit
- Nach jeder Antwort sofort in die Ziel-Datei schreiben (nicht batchen)
- Bei "weiß nicht" → `<!-- TBD -->` markieren, weiter
- Nichts erfinden — nur Antworten des Nutzers persistieren
- HTML-Header-Kommentare in den Templates bleiben unangetastet

## Abbruch und Wiederaufnahme

`/onboard` darf abgebrochen werden. Bei erneutem Aufruf prüfen, welche Dateien schon echten Inhalt haben (keine `<!-- TBD -->` und keine leeren Sektionen) und mit der ersten unvollständigen Phase starten.

## Promotion / Folgeschritte

Nach abgeschlossenem Onboarding ist der Nutzer **noch nicht** "produktiv eingerichtet" — er ist *kontext-eingerichtet*. Sinnvolle Folgeschritte:

- Erstes Projekt via `project-bootstrapper`-Agent
- `/grill-me` für die erste echte Entscheidung
- Optional: MCP-Server in `.mcp.json` aktivieren (Anleitung in `references/tools/`)

## Friction-Review

Nach den ersten 5–10 Onboarding-Läufen (eigene oder von Kollegen) prüfen:

- Welche Frage führt häufig zu "weiß nicht"? → Bessere Vorlage oder Beispiel
- Welche Datei wird nie ausgefüllt? → Streichen oder optional machen
- Welche Sektion füllt der Nutzer immer ähnlich? → Vorausgefüllter Default

Anpassungen direkt in `.claude/commands/onboard.md` und in die entsprechenden `context/*.md` Templates einfließen lassen.
