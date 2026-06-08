# Session Start

Starte die Arbeitssession mit einem klaren Überblick.

## Schritt 0 — Wochentag prüfen

Das heutige Datum steht im `currentDate`-Kontext.

**Ist heute Montag?**

- **JA** → Führe das Montags-Protokoll durch (alle Schritte), dann zeige den Session-Start.
- **NEIN** → Direkt mit "Dateien lesen" weiter.

---

## Montags-Protokoll

### Lies zuerst

1. `context/current-priorities.md`
2. `operations/inbox.md`
3. `operations/roadmap.md`
4. `operations/weekly-review.md` (oberster Eintrag — was war letzte Woche dran)

### 1 — Letzte Woche (AUTO — keine Frage)

Lies den letzten Eintrag in `operations/weekly-review.md`. Fasse die erledigten Punkte selbst zusammen — max. 3 Bullet Points. Wenn der letzte Eintrag älter als 7 Tage ist, weise darauf hin.

Ausgabeformat:

```
## 📅 Wochenstart — KW [X], [DATUM]

### Letzte Woche
- ...
- ...
- ...
```

### 2 — Prioritäten setzen (INTERAKTIV)

Lies alle offenen Punkte aus `operations/inbox.md` und die aktuellen Schwerpunkte aus `context/current-priorities.md`.
Wähle die 4 wichtigsten/dringendsten als Optionen.
Nutze `AskUserQuestion` mit `multiSelect: true` — maximal 4 Optionen:

Frage: **"Was sind deine Schwerpunkte diese Woche?"**

### 3 — Wochenplan anzeigen

```
## 📋 Wochenplan KW [X]

**Gewählte Schwerpunkte**
- [Angeklickte Prios aus Schritt 2]

**Heute starten wir mit:**
[Eine konkrete Aufgabe — die wichtigste aus den Schwerpunkten]
```

### 4 — Freigabe

Frage: "Passt der Plan so?" → Warte auf "ja" oder Korrekturen.

### 5 — Schreiben

- Neuen Abschnitt oben in `operations/weekly-review.md` mit Datum + Wochenplan
- `context/current-priorities.md` mit den gewählten Schwerpunkten aktualisieren

---

## Dateien lesen (immer — auch nach Montags-Protokoll)

1. `context/current-priorities.md`
2. `operations/inbox.md`
3. Wenn ein aktives Projekt im Fokus ist: dessen `projects/active/<projekt>/README.md` + `status.md`

Gib danach diesen strukturierten Tagesstart aus — kurz, klar, ohne Fließtext:

---

## ☀️ Session Start — [DATUM]

### 🎯 Fokus diese Woche
(Top 2–3 Punkte aus current-priorities.md, nummeriert)

### 🔴 Heute offen
(Offene Tasks aus inbox.md — nummeriert, maximal 5, konkret formuliert)

### 📥 Inbox
(Falls inbox.md weitere Einträge hat: Anzahl + wichtigster Punkt. Falls leer: "Inbox leer ✓")

### ❓ Was ist dein Fokus heute?
(Stelle diese eine Frage und warte auf Antwort — dann direkt loslegen)

---

**Wichtig:** Kein Fließtext. Keine Einleitung. Direkt mit der Ausgabe starten.
Bullet Points, Fettschrift für Schlüsselinfos, maximal 1 Satz pro Punkt.

$ARGUMENTS
