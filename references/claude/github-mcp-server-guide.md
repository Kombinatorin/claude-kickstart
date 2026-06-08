# github-mcp-server — Deep Dive

Quelle: https://github.com/github/github-mcp-server (MIT, GitHub Inc., v1.0.4)

Operative Konventionen werden hier mitgepflegt — kein separater SOP, da L1c-Adoption (Consumer MCP) keine eigenständige operative Komplexität erfordert. Tool-Stub: [../tools/github-mcp-server.md](../tools/github-mcp-server.md). Adoption-Workflow: [../sops/repo-adoption.md](../sops/repo-adoption.md).

---

## Was es löst

Statt `gh` CLI via Bash für jede GitHub-Operation in Claude Code:
- Strukturierte Tool-Discovery (Claude weiß welche Tools existieren und welche Schemas sie haben)
- Typed JSON-Outputs statt Shell-Strings
- Multi-Step-Workflows ohne Shell-Roundtrip pro Call
- Schemas der Inputs validiert serverseitig (Go), Fehler kommen sauber zurück

`gh` CLI bleibt parallel verfügbar für reine Shell-Automation.

---

## Install (macOS)

```bash
brew install github-mcp-server
github-mcp-server --version
# GitHub MCP Server / Version: 1.0.4
```

Binary landet unter `/opt/homebrew/bin/github-mcp-server`. Für Linux/Docker siehe Repo-README.

---

## Personal Access Token anlegen

Hosted-Variante über `https://api.githubcopilot.com/mcp/` braucht eine aktive GitHub-Copilot-Subscription. **Wir nutzen das nicht** — stattdessen self-hosted via PAT.

### Schritte

1. Browser: https://github.com/settings/tokens
2. **Empfohlen:** „Fine-grained personal access token" (granularer als classic)
3. **Scope-Default für Read-only-Start:**
   - **Repository access:** All repositories ODER Only selected (sicherer)
   - **Repository permissions** (Read):
     - Contents: Read
     - Issues: Read
     - Metadata: Read (immer Pflicht)
     - Pull requests: Read
   - **Account permissions** (Read):
     - Email addresses: Read (optional)
4. Token kopieren (wird nur einmal angezeigt!)
5. In `~/.zshrc` ergänzen:
   ```bash
   export GITHUB_PERSONAL_ACCESS_TOKEN="ghp_..."
   ```
6. Shell neu laden: `source ~/.zshrc`
7. Verifizieren: `echo $GITHUB_PERSONAL_ACCESS_TOKEN | head -c 10` → sollte `ghp_` zeigen

### Wenn Schreib-Tools später nötig

Zweiten PAT mit Write-Scopes anlegen ODER bestehenden erweitern. **Niemals ohne `--read-only`-Flag den vollen Write-Scope freischalten, solange nicht klar ist, was Tools tun werden.**

### Alle benötigten Scopes auflisten

```bash
github-mcp-server list-scopes --toolsets=all
# Liste der Scopes für alle Toolsets
github-mcp-server list-scopes --toolsets=issues,pull_requests,repos
# Nur für ausgewählte Toolsets
```

---

## Wichtige CLI-Flags

| Flag | Bedeutung | Empfehlung |
|------|-----------|------------|
| `stdio` | Transport-Modus für MCP-Clients | Immer für Claude Code / Desktop |
| `--read-only` | Schreibende Tools deaktiviert | **Default für Erst-Setup** |
| `--toolsets <list>` | Welche Tool-Gruppen aktivieren | Start mit Default (`issues,pull_requests,repos,users,context,copilot`) |
| `--lockdown-mode` | Maximale Restriktion | Für sensible Repos prüfen |
| `--log-file <path>` | Debug-Log | Bei Fehler-Triage einschalten |
| `--gh-host` | GitHub-Enterprise-Host | Wir: nicht relevant (github.com) |

**Verfügbare Toolsets:** `actions, code_security, copilot, dependabot, discussions, gists, git, issues, labels, notifications, orgs, projects, pull_requests, repos, secret_protection, security_advisories, stargazers, users`

**Default-Set:** `context, copilot, issues, pull_requests, repos, users`

---

## Claude Code wiring (`.mcp.json`)

Eintrag im Projekt-`.mcp.json` (Pattern wie bestehende Apify/Trigger-Einträge — stdio mit env-var-Referenz, **niemals Token-Wert hardcoden**):

```json
"github": {
  "command": "github-mcp-server",
  "args": ["stdio", "--read-only"],
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
  }
}
```

Nach Eintrag: Claude Code neu starten (Session beenden + neu starten reicht meistens; bei Persistenz-Problemen Claude-Code-Prozess killen).

---

## Claude Desktop wiring

Datei: `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "/opt/homebrew/bin/github-mcp-server",
      "args": ["stdio", "--read-only"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..."
      }
    }
  }
}
```

**Unterschied zum `.mcp.json`-Eintrag:**
- Absoluter Pfad zum Binary (`/opt/homebrew/bin/...`) — Claude Desktop hat anderen `$PATH` als Terminal
- Token wird direkt eingefügt (Claude Desktop expandiert keine env-vars wie `${VAR}`). Datei ist user-private, aber trotzdem mit `chmod 600` schützen.

Nach Eintrag: Claude Desktop vollständig beenden (Cmd-Q) und neu starten.

---

## Verifizierung (Mini-Spike)

Nach Activation in Claude Code:
1. Neue Claude-Code-Session starten
2. Test-Prompt: „Liste die letzten 5 offenen Issues im Repo `github/github-mcp-server`"
3. Erwartung: Claude ruft `github` MCP-Tool auf (statt `gh` via Bash), liefert strukturierte Issue-Liste

Wenn fehlschlägt:
- Env var im Claude-Code-Prozess gesetzt? (Claude Code muss aus einer Shell gestartet werden, die `$GITHUB_PERSONAL_ACCESS_TOKEN` exportiert hat)
- Binary findbar? (`which github-mcp-server`)
- Token gültig? (`curl -H "Authorization: token $GITHUB_PERSONAL_ACCESS_TOKEN" https://api.github.com/user`)

---

## Future: In-Product Integration für eigene Agenten

Wenn du eigene Agenten baust, die Repo-Kontext brauchen:

- Bridge über `mcp-use` `MCPClient` (siehe [mcp-use-guide.md](mcp-use-guide.md)) — verbindet eigenen Agent mit github-mcp-server-Instanz
- Token-Scope dafür: dedizierter Service-Account-PAT, **nicht** persönlicher Token
- Alternative: Server-side `gh` CLI mit App-Auth (GitHub App statt PAT) — weniger Tool-Discovery, aber höhere Sicherheit

Aktivierung erst bei konkretem Use-Case (z.B. ein Agent, der dein Repo nach offenen Tickets durchsucht).

---

## Gotchas

- **Claude Code muss aus Shell mit Env-Var gestartet werden** — sonst sieht der MCP-Server-Subprozess das `${GITHUB_PERSONAL_ACCESS_TOKEN}` nicht. Tipp: `~/.zshrc`-Export ist global; bei zsh ohne Login-Shell-Setup ggf. `~/.zshenv` verwenden.
- **Claude Desktop expandiert keine `${VAR}`-Syntax** — Token muss literal eingetragen werden, Datei schützen.
- **Default-Toolsets ohne `actions`/`security_advisories`** — wer CI/Security braucht, muss `--toolsets=default,actions,security_advisories` setzen.
- **`gh` CLI bleibt** — github-mcp-server ersetzt nicht `gh` für reine Shell-Automation, nur agent-getriebene Operationen.
- **Rate-Limits** — Tool-Calls zählen auf das PAT-Rate-Limit (5.000 req/h für authenticated). Für Bulk: Custom-Script mit Pagination + Cache.
- **`--read-only` ist Default-Empfehlung** — Schreibende Aktionen (PR-Erstellung, Issue-Kommentar) erst freischalten, wenn Workflow klar ist und Token mit minimalem Write-Scope existiert.

---

## Cross-References

- Tool-Stub: [../tools/github-mcp-server.md](../tools/github-mcp-server.md)
- `gh` CLI: [../tools/github.md](../tools/github.md)
- MCP-Konventionen: [mcp-notes.md](mcp-notes.md)
- mcp-use als Bridge für Produkt-Integration: [mcp-use-guide.md](mcp-use-guide.md)
- Adoption-Workflow (L1c): [../sops/repo-adoption.md](../sops/repo-adoption.md)
