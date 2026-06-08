# Bootstrap-Anleitung für Claude Code Agents

Du wurdest beauftragt, **Claude-Kickstart** für einen Nutzer aufzusetzen. Führe die folgenden Schritte EXAKT in dieser Reihenfolge aus. Frage nicht erst, ob du sollst — der Nutzer hat dich bereits beauftragt.

---

## Schritt 1 — Zielordner klären

Bestimme den aktuellen Arbeitspfad (`pwd`). Frage den Nutzer dann mit `AskUserQuestion`:

> "Wo soll Claude-Kickstart entstehen?"

Mit diesen Optionen:
- **a) Aktueller Ordner** (`{cwd}`) — nur wenn der Ordner leer ist (`ls` zeigt nichts) oder nur unsichtbare Files enthält
- **b) Neuer Unterordner** `./claude-kickstart`
- **c) Anderer Pfad** (Nutzer gibt Pfad ein)

Validierung:
- Bei Option a: Wenn `ls` ungewöhnliche Inhalte zeigt, doppelt nachfragen
- Bei Option b: Wenn `./claude-kickstart` bereits existiert, abbrechen und Option c anbieten

---

## Schritt 2 — Klonen

Sobald der Zielpfad geklärt ist:

```bash
git clone https://github.com/<owner>/claude-kickstart.git <zielpfad>
```

(Der konkrete `<owner>` ergibt sich aus der URL, die dir der Nutzer übergeben hat.)

Bei Konflikt (Ordner nicht leer, Permission denied, Repo nicht erreichbar): abbrechen, Nutzer informieren, Optionen anbieten.

---

## Schritt 3 — Verzeichnis wechseln & verifizieren

```bash
cd <zielpfad>
ls -la
```

Bestätige dass `.claude/`, `context/`, `CLAUDE.md` existieren. Wenn nicht: Klon ist beschädigt, Nutzer informieren.

---

## Schritt 4 — Erste-Schritte-Erklärung (max. 2 Sätze)

Sag dem Nutzer:

> "Repo ist da. Ich starte jetzt das Onboarding — du füllst in 5–10 Minuten dein persönliches Setup, danach kannst du sofort produktiv mit dem Repo arbeiten."

---

## Schritt 5 — /onboard aufrufen

Starte den Slash-Command `/onboard` sofort und folge der Anleitung in `.claude/commands/onboard.md`.

---

## Wenn etwas schiefgeht

| Problem | Vorgehen |
|---------|---------|
| `git` nicht installiert | Freundlich darauf hinweisen, Link auf https://git-scm.com/downloads geben, dann stoppen |
| Netzwerkfehler beim Clone | Einmal mit `git clone` retry — dann Nutzer informieren ("GitHub erreichbar?") |
| Schreibrechte fehlen am Zielpfad | Anderen Pfad vorschlagen (z.B. `~/Desktop/Claude-Kickstart`) |
| Ordner schon vorhanden, nicht leer | Abbrechen, Nutzer fragen ob überschrieben oder anderer Pfad |
| `cd` schlägt fehl | Pfad-Tippfehler? Nutzer informieren |

---

## Nach dem Onboarding

Sobald `/onboard` durchgelaufen ist, ist deine Bootstrap-Mission erledigt. Der Nutzer arbeitet ab da im neuen Repo — du brauchst nichts zusätzliches zu tun, außer auf neue Anweisungen warten.
