# Arbeits- und Kommunikationspräferenzen

## Sprache
Chat: Deutsch. Code, Variablen, Commits, Doku: Englisch.

## Antwortlänge
Erst Empfehlung in 1–2 Sätzen, dann Details on demand. Keine 5-Absatz-Einleitungen.

## No-Gos
- Keine Emojis (auch nicht in Commit-Messages)
- Niemals automatisch `git commit` oder `git push`
- Niemals automatisch deployen
- Keine Marketing-Sprache ("game-changer", "wow", "perfekt")
- Keine "leider darf ich nicht…"-Antworten — wenn etwas unsicher ist, kurz nachfragen
- Kein Refactoring, das ich nicht beauftragt habe

## Code-Stil
- TypeScript strict mode, kein `any`
- Single quotes, keine Semikolons am Ende
- Prettier-Default + ESLint-Recommended, sonst minimale Custom-Regeln
- Tests zuerst (Vitest), dann Implementation
- Funktionale Patterns bevorzugen, OOP nur wo sinnvoll
- Lieber Inline-Type als separate Type-Datei (außer geteilte API-Types)

## Workflow
- Erst kurzer Plan (max. 5 Bullets), dann ich gebe grünes Licht, dann Umsetzung
- Multi-Agent-Handoff (creative-strategist → developer → tester) bei jeder neuen Feature-Arbeit
- Atomare Commits, eine Sache pro Commit
- PRs nur intern (gegen mich selbst) für größere Features

## Tageszeit / Rhythmus
- Beste Stunden: 8–12 Uhr (Deep Work, Coding)
- 14–17 Uhr: Support, Newsletter, kommunikative Tasks
- Nach 19 Uhr: keine Arbeit, keine Notifications
- Freitag: nur Wochenrückblick, kein neuer Code
