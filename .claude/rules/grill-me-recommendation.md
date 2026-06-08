# Grill-Me Recommendation Rule

## Zweck

Eine **einmalige, sanfte** Empfehlung pro Conversation, falls Claude einen Grill-Me-Case erkennt. Der Skill (`/grill-me`) wird **niemals** automatisch ausgelöst — nur empfohlen.

## Wann eine Empfehlung formulieren

Wenn **≥2** der folgenden Komplexitätssignale gleichzeitig auftreten **und** in dieser Conversation noch keine Grill-Me-Empfehlung ausgesprochen wurde:

- Sprache: „lass uns überlegen", „ich weiß nicht genau", „mehrere Optionen", „was denkst du"
- Thema berührt ≥2 `context/*.md` Bereiche
- Neues Modul, Pilot, Projekt-Aufsetzen oder strategischer Pivot
- ≥3 offene Entscheidungen in einem Prompt

## Format der Empfehlung

> „Das fühlt sich nach einem Grill-Me-Case an (Gründe: X, Y). Willst du `/grill-me <topic>` starten?"

Kurz, eine Frage, kein Drängeln.

## Verhalten nach User-Antwort

- **User sagt ja** → Skill via `/grill-me <topic>` triggern, normal weiter
- **User sagt nein / ignoriert** → In dieser Conversation **nicht erneut** empfehlen. Normal weiterarbeiten als wäre die Empfehlung nie gewesen.

## Wann NIE empfehlen (silent default)

- Konkrete Coding-Tasks
- Single-Action-Requests („deploy", „commit", „lies File X", „push das")
- Recherche, Lookups, Suchen
- Mini-Fragen mit klarer Antwort

## Verweis

Vollständige Beschreibung des Skills und der Promotion-Pfade: `references/sops/grill-me.md`
