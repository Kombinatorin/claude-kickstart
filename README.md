# Claude-Kickstart

**Ein Starter-Repo für alle, die mit Claude Code anfangen — und sofort ein durchdachtes, modulares Operating-Model nutzen wollen.**

Statt vor einer leeren `.claude/` zu stehen und sich Strukturen, Skills und Rituale mühsam selbst zusammenzubauen, klonst du dieses Repo, beantwortest 10 Minuten lang ein paar Fragen, und bist startklar.

---

## 🤖 Hinweis für Claude Code Agents

**Wenn ein Nutzer dir diese URL gegeben hat mit einer Setup-Bitte** ("mach bitte", "setze auf", "installier das", "bau mir das auf" o.ä.), folge **exakt** der Anleitung in [BOOTSTRAP.md](BOOTSTRAP.md). Frage nicht erst, ob du sollst — der Nutzer hat dich bereits beauftragt.

---

## So startest du (für Menschen)

Öffne Claude (Claude Code in einem Ordner, oder claude.ai im Browser), füge diese Repo-URL in den Chat ein und schreib dazu **"mach bitte"** — Claude erledigt den Rest:

```
https://github.com/<owner>/claude-kickstart — mach bitte
```

Was Claude dann macht:

1. Fragt dich, wo das Repo entstehen soll
2. Klont es dorthin
3. Startet den geführten Setup-Flow (`/onboard`)
4. Du beantwortest 7 kurze Themenblöcke (je 3–6 Fragen)
5. Fertig — dein persönliches Operating-Model ist live

**Voraussetzung:** `git` muss auf deinem Rechner installiert sein. Auf macOS/Linux ist es das meistens schon — sonst kurz von [git-scm.com/downloads](https://git-scm.com/downloads) holen.

### Manueller Weg (falls du es lieber selbst klonst)

```bash
git clone https://github.com/<owner>/claude-kickstart.git
cd claude-kickstart
claude          # Claude Code starten
/onboard        # Setup-Flow starten
```

---

## Was du bekommst

| Bereich | Was drin ist |
|---------|--------------|
| **Regeln** | 8 generische Operating-Regeln (Communication, File-Governance, Tool-Access, Handoff-Protokoll …) |
| **Agents** | 4 Spezialist-Rollen: Bootstrapper, Strategist, Developer, Tester — mit klarem Multi-Agent-Workflow |
| **Skills** | `/grill-me` — relentless Interview-Modus mit sofortiger Persistenz |
| **Commands** | `/onboard`, `/session-start`, `/session-end`, `/weekly-review` |
| **Context-Templates** | 7 vorbereitete `context/*.md` mit Inline-Coaching-Fragen |
| **Beispiele** | Fiktive Persona "Sam, Indie-Developer" — sieh wie ein vollständig befülltes Kit aussieht |
| **Operations-Rituale** | Inbox, Roadmap, Maintenance, Weekly-Review als gepflegte Markdown-Routinen |
| **Tool-Referenzen** | Generische Quick-Refs für Apify, Firecrawl, GitHub, Supabase, Stripe, Trigger.dev, Vercel etc. |
| **Project-Template** | Standardstruktur für jedes neue Projekt unter `projects/active/` |

---

## Du darfst alles ändern

Was du hier siehst, ist ein durchdachter **Vorschlag** — kein Käfig. Wenn dir eine Datei nicht passt, schreib sie um. Wenn ein Skill nicht hilft, lösch ihn. Das Repo gehört dir, ab dem Moment in dem du es klonst.

Die Update-Schleife dafür steht in [`.claude/rules/update-feedback-loops.md`](.claude/rules/update-feedback-loops.md).

---

## Struktur (Kurzfassung)

```
.
├── CLAUDE.md              ← Schlanke Kontrolldatei, Einstiegspunkt für Claude
├── BOOTSTRAP.md           ← Anweisung für Claude beim allerersten Setup
├── .claude/               ← Rules, Agents, Skills, Slash-Commands
├── context/               ← Deine 7 Kontext-Dateien (via /onboard befüllen)
├── references/            ← Claude-, Tool-, Design-Refs + SOPs + Beispiele
├── operations/            ← Inbox, Roadmap, Maintenance, Weekly-Review
├── decisions/             ← Append-only Decision Log
├── templates/             ← Wiederverwendbare Markdown-Vorlagen
└── projects/              ← active/, archive/, _templates/project-template/
```

Detail-Erklärung jeder Ebene in [`CLAUDE.md`](CLAUDE.md).

---

## Lizenz

[MIT](LICENSE) — nutze es privat oder kommerziell, mit oder ohne Attribution.

---

## Feedback

Wenn du das Repo nutzt und etwas reibt — auf GitHub ein Issue öffnen oder die reibende Stelle direkt forken & PR. Beides ist gleich willkommen.
