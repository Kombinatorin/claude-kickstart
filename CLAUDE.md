# Claude-Kickstart

Modulares Operating-Model für Claude Code. Ein Datei = ein Zweck. Verweise statt Duplikate. Klare Struktur statt Improvisation.

> **Du darfst alles ändern.** Was du hier siehst, ist ein durchdachter Vorschlag — kein Käfig. Wenn dir eine Datei nicht passt, schreib sie um. Wenn ein Skill nicht hilft, lösch ihn. Das Repo gehört dir, ab dem Moment in dem du es klonst.

---

## First-Run-Erkennung

**Hinweis an Claude (lies das vor der ersten Antwort in diesem Repo):**

Wenn `context/me.md` noch den `<!-- Vorschlag, keine Vorschrift -->`-Header enthält UND noch keine echten Antworten unter den Frage-Stichpunkten stehen, ist dieses Repo frisch geklont. Schlage dem Nutzer als Erstes vor, `/onboard` zu starten — der geführte Aufbau seiner Kontext-Dateien.

Sobald `context/me.md` echten Inhalt hat, ist der Onboarding-Vorschlag nicht mehr nötig.

---

## Regeln (alles unter `.claude/rules/`)

| Datei | Zweck |
|-------|-------|
| `operating-principles.md` | Arbeitsweise: modular, ein Zweck pro Datei, Referenzen vor Duplikaten |
| `file-governance.md` | Wo welche Datei lebt, Größenlimits, Anti-Bloat |
| `communication-style.md` | Klar, strukturiert, direkt — keine Hype-Sprache |
| `tool-access-policy.md` | Welche Rolle darf welches Werkzeug nutzen |
| `reference-policy.md` | Quellen-Priorität (offizielle Docs zuerst) |
| `update-feedback-loops.md` | Wie das System auf Tool-/API-Änderungen reagiert |
| `handoff-protocol.md` | Multi-Agent-Übergabe (STRATEGY → IMPL → VALIDATION) |
| `grill-me-recommendation.md` | Wann `/grill-me` empfehlen, wann schweigen |

---

## Agenten (`.claude/agents/`)

| Agent | Rolle | Modell |
|-------|-------|--------|
| `project-bootstrapper` | Legt neue Projekt-Ordner aus dem Template an | haiku |
| `creative-strategist` | Recherchiert Optionen, schreibt STRATEGY.md | opus |
| `developer` | Implementiert nach STRATEGY.md, schreibt IMPL-NOTES.md | sonnet |
| `tester` | Validiert gegen STRATEGY.md, schreibt VALIDATION.md | sonnet |

**Model-Policy:** Default ist `claude-sonnet-4-6` (in `.claude/settings.json`). Eskaliere auf Opus via `/model opus` bei Architektur-Entscheidungen, Strategie-Sessions oder komplexem Debugging. Haiku für reine Datei-/Template-Operationen.

---

## Skills (`.claude/skills/`)

| Skill | Aufruf | Zweck |
|-------|--------|-------|
| `grill-me` | `/grill-me <thema>` | Relentless-Interview-Modus mit sofortiger Persistenz nach `operations/brainstorms/` |

SOPs für Skills: `references/sops/`.

---

## Slash-Commands (`.claude/commands/`)

| Command | Zweck |
|---------|-------|
| `/onboard` | Geführter Erst-Aufbau der `context/*.md`-Dateien (nur 1x pro Repo nötig) |
| `/session-start` | Tagesplan zum Sessionbeginn, liest Kontext + Inbox |
| `/session-end` | Session-Summary + Inbox-Update am Sessionende |
| `/weekly-review` | Wochenrückblick + Lessons Learned |

---

## Kontext (`context/`)

| Datei | Inhalt |
|-------|--------|
| `me.md` | Wer du bist, Rolle, Arbeitsstil |
| `work.md` | Was du beruflich machst, Produkte, Domänen |
| `goals.md` | Quartals- und Jahresziele |
| `preferences.md` | Kommunikations-No-Gos, Sprache, Code-Stil |
| `tools-stack.md` | Welche Werkzeuge du nutzt |
| `team.md` | Team oder Solo |
| `current-priorities.md` | Aktuelle Foki |

Befüllung via `/onboard` (geführt) oder direkt im Editor.

---

## Referenzen (`references/`)

| Ort | Inhalt |
|-----|--------|
| `claude/` | Offizielle Claude/MCP/Subagents-Docs und Notizen |
| `tools/` | Pro-Tool-Referenzen (Apify, Firecrawl, GitHub, Supabase, Stripe, Trigger.dev, Vercel, …) |
| `design/` | UI-, Web- und Print-Design-Guidelines |
| `sops/` | Standardprozeduren (grill-me, mcp-use, repo-adoption) |
| `examples/` | Beispiele und Beispiel-Persona als Anschauungsmaterial |

---

## Operations (`operations/`)

| Datei | Zweck |
|-------|-------|
| `inbox.md` | Capture für lose Tasks und Ideen |
| `roadmap.md` | Initiativen-Pipeline auf Quartals-Ebene |
| `maintenance.md` | Recurring Ops & Wartungsroutinen |
| `weekly-review.md` | Logbuch der wöchentlichen Reviews |
| `brainstorms/` | Output-Verzeichnis für `/grill-me` und `/onboard` (nicht versioniert) |

---

## Projekte (`projects/`)

- Aktiv: `projects/active/`
- Archiv: `projects/archive/`
- Template: `projects/_templates/project-template/`

Anlegen via `project-bootstrapper`-Agent oder manuell aus dem Template.

---

## Entscheidungen (`decisions/`)

- `log.md` — Append-only Decision Log
- Eintragsformat: `templates/decision-entry.md`

---

## Templates (`templates/`)

`decision-entry.md` · `handoff-template.md` · `project-readme.md` · `project-status.md` · `task-brief.md`

---

## Anpassen, nicht akzeptieren

Wenn dieses Repo nach 4 Wochen Nutzung an irgendeiner Stelle reibt, ändere die reibende Stelle. Die Update-Schleife dafür steht in `.claude/rules/update-feedback-loops.md`.
