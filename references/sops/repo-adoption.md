# SOP: External Repo Adoption

Standardisierter Workflow für „interessantes GitHub-Repo gesichtet → integrierte Capability in AI.Instructor".

## Trigger

Wende diesen SOP an, wenn:
- ein externes Repo (GitHub, GitLab) als Wissens- oder Tool-Quelle in Frage kommt
- ein bestehender Capability-Gap auftaucht (z.B. „eigenen MCP-Server bauen", „neuer Crawler", „Auth-Pattern")
- du wiederkehrend dieselbe Klasse von Code/Wissen suchst

Nicht anwenden für: einmaliges Code-Snippet copy-pasten ohne Folge-Nutzen.

---

## Step 1 — Evaluate (≤ 15 min)

Schnell-Check anhand der folgenden Tabelle. **Wenn ein Kriterium rot ist und nicht heilbar → STOP, dokumentieren als „rejected".**

| Kriterium | OK | Warnen | STOP |
|-----------|----|----|------|
| License | MIT, Apache-2.0, BSD | LGPL, MPL (Re-distribution prüfen) | GPL/AGPL bei kommerzieller Nutzung, proprietär |
| Activity | Commit < 6 Monate, Issues responded | < 12 Monate, Issues vorhanden | > 12 Monate, archiviert, ghost-maintainer |
| Stack-Fit | Node/TS, Python, läuft auf Vercel/Hostinger | Andere Sprache + sauberer Container möglich | Erfordert Rewrite des bestehenden Stacks |
| Maintainer | Org oder Funding (NumFOCUS, Backers) | Aktiver Solo-Maintainer | Inaktiver Solo-Maintainer ohne Backup |
| Alternativen | Klar besser als Status quo / etablierte Lösung | Marginal besser | Erfindet das Rad neu |
| Security | Audited, signed releases | Standard npm/pip | Bekannte CVEs offen, Supply-Chain-Risiko |

**Output Step 1:** 5-Zeilen-Notiz (mental oder direkt im Tool-Stub) — Repo-URL, License, Last-Commit, Stack, GO/WARN/STOP.

---

## Step 2 — Decide Integration Level

Wähle das niedrigste sinnvolle Level. **Du kannst später upgraden, aber nicht ungeschehen machen, was du gebaut hast.**

| Level | Was bedeutet das | Artefakte |
|-------|------------------|-----------|
| **L1 — Knowledge-only** | Patterns lesen, lernen, Snippets ablegen. Keine Installation, keine Abhängigkeit. | `references/tools/<name>.md` + optional `references/claude/<name>-guide.md` |
| **L1c — Consumer MCP Server** | Fertiger MCP-Server (oft offiziell vom Anbieter), den wir per Client-Config konsumieren — kein eigener Code, kein Hosting eigener Logik | `references/tools/<name>.md` + `references/claude/<name>-guide.md` + Eintrag in `.mcp.json` (Claude Code) und/oder `claude_desktop_config.json` (Claude Desktop) |
| **L2 — Library/Tool** | Als Dependency installieren, in eigenen Code einbauen, kein Hosting | + `references/sops/<name>.md` + Spike unter `projects/active/spikes/<name>/` |
| **L3 — Fork/Host** | Eigener Fork, eigenes Deployment, ggf. Anpassungen | + vollständiges Projekt unter `projects/active/<name>/` + Decision-Log-Eintrag pflicht |

**Faustregel:** Wenn du nicht zwei konkrete Anwendungsfälle in den nächsten 4 Wochen nennen kannst → L1.

**L1c-Hinweis:** Für Consumer-MCP-Server entfällt der Spike-Code; der Spike reduziert sich auf Auth-Setup + 1 echter Tool-Call (≤ 30 min). Auth läuft entweder hosted via OAuth oder self-hosted via Personal Access Token (env var, nie hardcoded).

---

## Step 3 — Extract & Document

Lege die Dokumente in der Reihenfolge an, abhängig vom Level:

### Tool-Stub (alle Level)

`references/tools/<name>.md` — Format wie [campai-mcp.md](../tools/campai-mcp.md) oder [stripe-api-reference.md](../tools/stripe-api-reference.md):
- Location / Repo-URL
- 1-Satz-Beschreibung
- Stack-Tags (Python, TS, MIT, …)
- Link auf Deep-Dive falls vorhanden

### Deep-Dive (L1+, wenn substantiell)

`references/claude/<name>-guide.md`:
- Install-Snippet (kopierbar, lauffähig)
- Core-Konzepte (max. 5 Bullets)
- Code-Beispiel (das eine Beispiel, das du wirklich verstanden hast)
- Gotchas / Wo schießt man sich ins Knie
- Cross-References auf bestehende Notizen ([mcp-notes.md](../claude/mcp-notes.md) etc.)

### Operative SOP (L2+)

`references/sops/<name>.md` — Format wie [kb-bot.md](kb-bot.md):
- **Deployment Gate** (Checkliste vor Prod-Verwendung)
- **Failure Triage** (Symptom-Tabelle → Ursache → Fix)
- **Source Location** (lokaler Pfad)
- **Shared Infrastructure** (Supabase, Trigger.dev `proj_<your-project-id>`, Vercel)
- **Authentication** + Env-Vars
- Spezifika (siehe „RAG-Grounding" in [legal-kb.md](legal-kb.md))

---

## Step 4 — Pilot Spike (nur L2 / L3)

Pfad: `projects/active/spikes/<name>/`

**Inhalt eines Spikes (immer):**
- `README.md` — Hypothese, Erfolgskriterien (max. 3), Stop-Condition, Zeitbox (≤ 1 Tag)
- Minimaler lauffähiger Test (kein Boilerplate-Overkill)
- `SPIKE-RESULT.md` am Ende — GO / NO-GO mit Begründung, gemessener Aufwand, Vergleich gegen Status quo

**Erfolgskriterien-Template:**
1. Setup-Zeit < X Minuten
2. Kern-Funktionalität gegen echten Use-Case (nicht Hello-World) funktioniert
3. Integration mit bestehender Infrastruktur (Auth, env, Supabase) ist möglich

**Stop-Condition-Template:**
„Wenn nach Zeitbox X die Erfolgskriterien nicht erfüllt sind → Downgrade auf L1 oder Reject."

---

## Step 5 — Promote oder Deprecate

### Promote (GO)

- [ ] `decisions/log.md` Eintrag (Format: `[YYYY-MM-DD] DECISION: ... | REASONING: ... | CONTEXT: ...`)
- [ ] `CLAUDE.md` Integrations-Tabelle erweitern (nur L2/L3)
- [ ] Spike-Ordner archivieren oder zu echtem Projekt promoten
- [ ] Optional: Subagent oder Skill anlegen, wenn wiederholbar genug

### Deprecate (NO-GO)

- [ ] SOP-Datei in `references/sops/<name>.md` umwandeln in „Evaluated, rejected because X" — **nicht löschen**, damit du in 6 Monaten nicht erneut evaluierst
- [ ] Decision-Log-Eintrag mit Begründung

---

## Failure Triage

Was tun, wenn ein adoptiertes Repo nach der Übernahme Probleme macht:

| Symptom | Ursache | Fix |
|---------|---------|-----|
| Repo plötzlich archiviert / Maintainer weg | Maintenance-Death | SOP auf „needs-replacement" markieren, Alternativen-Scan starten |
| License-Change in neuer Major-Version | Re-Licensing (z.B. zu BSL) | Auf letzte freie Version pinnen, parallel migrieren |
| Breaking Changes ohne Deprecation | Schlechte Semver-Disziplin | Version pinnen, Major-Upgrade nur in eigenem Spike |
| Security-Advisory / CVE | Supply-Chain-Risiko | Sofort patchen oder migrieren, Decision-Log |
| Stack-Drift (z.B. Python-only-Feature ohne TS-Pendant) | Library entwickelt sich anders | SOP aktualisieren, ggf. Level reduzieren |

---

## Source Location

Dieser SOP selbst: `references/sops/repo-adoption.md`

Verwandt:
- Bestehende SOPs: [kb-bot.md](kb-bot.md), [legal-kb.md](legal-kb.md), [social-media-crawl.md](social-media-crawl.md)
- MCP-Konventionen: [../claude/mcp-notes.md](../claude/mcp-notes.md)
- Decision-Log: `/decisions/log.md`
- Decision-Template: `/templates/decision-entry.md`

---

## Anti-Patterns

- **Sofort L3 ohne L1-Evaluation** — fast immer zu früh.
- **Kein Spike vor Production-Use** — auch wenn das Repo „offensichtlich gut" aussieht.
- **Repo löschen statt deprecaten** — verlierst den Lerneffekt für die nächste Evaluation.
- **Mehrere Repos gleichzeitig adoptieren** — eines nach dem anderen, sonst entstehen Halbintegrationen.
