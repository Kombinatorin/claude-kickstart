# MCP Foundations

Konzeptuelles Grundlagenwissen über das Model Context Protocol — für Einsteiger, die verstehen wollen, wie Claude mit externen Werkzeugen und Datenquellen kommuniziert.

---

## Was MCP ist

**Model Context Protocol** ist ein offener Standard, der KI-Modellen erlaubt, externe Werkzeuge und Datenquellen zu nutzen. Anthropic hat ihn entwickelt, er ist aber nicht Claude-exklusiv — andere KI-Clients (Cursor, Windsurf, Continue.dev) implementieren ihn ebenfalls.

Die Kernidee: Statt Claude alles aus dem Training wissen zu lassen, gibt man ihm Werkzeuge — definierte Funktionen, die es zur Laufzeit aufrufen kann, um echte Daten zu holen oder Aktionen auszuführen.

---

## Die 3 Rollen

```
┌──────────────────────────────────────────────────────────┐
│  MCP HOST                                                │
│  Der KI-Client, den der Nutzer verwendet.                │
│  Beispiele: Claude.ai, Claude Desktop, Cursor, n8n       │
└────────────────────────┬─────────────────────────────────┘
                         │ enthält einen
┌────────────────────────▼─────────────────────────────────┐
│  MCP CLIENT                                              │
│  Die Verbindungsschicht. Oft unsichtbar, im Host         │
│  eingebaut. Baut die Verbindung zum Server auf,          │
│  hält Tool-Listings und leitet Aufrufe weiter.           │
└────────────────────────┬─────────────────────────────────┘
                         │ verbindet sich mit
┌────────────────────────▼─────────────────────────────────┐
│  MCP SERVER                                              │
│  Dein Service. Stellt Tools, Resources und Prompts       │
│  bereit. Kann lokal laufen oder remote als Web-Service.  │
└──────────────────────────────────────────────────────────┘
```

**Ein Host kann mit mehreren Servern gleichzeitig verbunden sein.** Nutzer können z.B. GitHub-MCP + Supabase-MCP + Trigger.dev-MCP parallel in Claude.ai aktiv haben.

---

## Die 3 Server-Capabilities

MCP-Server können drei Dinge anbieten:

### 1. Tools (am wichtigsten)

Funktionen, die Claude **aktiv aufrufen** kann. Claude entscheidet selbst, wann ein Tool relevant ist — basierend auf der Tool-Beschreibung und dem Gesprächskontext.

```
Tool-Anatomie:
  name:        search_documents          ← snake_case, eindeutig
  description: "Durchsucht die           ← Claude liest das. Gute
               Wissensbasis nach          Beschreibung = bessere
               Stichwort oder Thema."     Tool-Nutzung durch Claude.
  inputSchema: { query: string,          ← JSON-Schema / Zod
                 limit?: number }
  → return:    { results: Document[] }   ← Claude verarbeitet den Output
```

### 2. Resources

Daten, die Claude **passiv lesen** kann — ähnlich wie Dateien. Kein aktiver Aufruf nötig, Claude zieht sie wenn relevant. Seltener genutzt als Tools.

### 3. Prompts

Vordefinierte Prompt-Templates, die der Server bereitstellt. Nutzer können sie aus dem Host aufrufen. Selten genutzt, oft überflüssig.

**Faustregel:** In den meisten produktiven MCP-Servern läuft alles über Tools.

---

## Transport-Typen — der kritische Unterschied

| | stdio | HTTP / SSE (Streamable HTTP) |
|---|---|---|
| **Wie** | Lokaler Prozess via stdin/stdout | Remote URL via HTTPS |
| **Wo nutzbar** | Nur Claude Desktop (lokal) | Überall: Web, Desktop, Mobile |
| **Anforderungen Nutzer** | App installieren + Config-File editieren | URL + API-Key kennen |
| **Anforderungen Server** | Läuft auf dem Gerät des Nutzers | Läuft als Web-Service |
| **Strenge IT-Umgebungen** | Oft geblockt | Funktioniert meistens |
| **Handy** | Nicht möglich | Funktioniert |

**Empfehlung:** Wenn dein MCP-Server von mehr als einer Person genutzt werden soll, baue ihn als HTTP/SSE-Server. stdio nur für rein lokale Entwickler-Tools.

---

## Wie Claude Tools nutzt — der interne Ablauf

1. Nutzer schreibt eine Nachricht
2. Claude sieht die Liste aller verfügbaren Tools (Name + Description)
3. Claude entscheidet: "Brauche ich ein Tool für diese Anfrage?"
4. Wenn ja: Claude formuliert einen Tool-Call mit den nötigen Parametern
5. Der Host schickt den Call an den MCP Server
6. Server führt aus, gibt Ergebnis zurück
7. Claude liest das Ergebnis und formuliert eine Antwort

**Claude ruft niemals ein Tool auf, das der Nutzer nicht freigeschaltet hat.** Jede MCP-Integration muss explizit aktiviert werden.

### Approval-Modi

Beim Verbinden eines MCP-Servers kann der Nutzer festlegen:

- **Auto-approve**: Claude darf alle Tools dieser Integration ohne Rückfrage aufrufen
- **Per-tool**: Claude fragt vor jedem Tool-Aufruf nach
- **Read-only**: Nur Tools mit `readOnlyHint=true` werden auto-approved

Für rein lesende Tools (Suche, Lookup) ist Auto-approve sinnvoll. Für schreibende Tools (Mail senden, Datensatz ändern) ist Per-tool die sichere Wahl.

---

## Wie Nutzer einen Remote-MCP-Server einbinden

### claude.ai Web (empfohlener Weg — keine Installation nötig)

```
claude.ai → Einstellungen → Integrationen → Integration hinzufügen
  → Name: <dein Server-Name>
  → URL:  https://<dein-server>/mcp
  → Auth: Bearer <API-Key>
  → Speichern → Tools erscheinen sofort im Chat
```

Geht auf jedem Gerät mit Browser. Kein Download, kein Config-File.

### Claude Desktop (für technikaffine Nutzer)

```json
// ~/Library/Application Support/Claude/claude_desktop_config.json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://<dein-server>/mcp",
        "--header",
        "Authorization: Bearer <api-key>"
      ]
    }
  }
}
```

Erfordert: Claude Desktop installiert, `npx` verfügbar, Datei editierbar.

### Claude Mobile App (iOS / Android)

Gleicher Weg wie claude.ai Web — über App-Einstellungen → Integrationen.

---

## Sicherheitsmodell

### Was MCP nicht kann (by design)

- Kein MCP-Server kann sich selbst in eine Claude-Session einschleusen
- Kein Tool wird aufgerufen ohne Nutzer-Freigabe der Integration
- Der Nutzer sieht welche Integrationen aktiv sind

### Was Betreiber beachten müssen

- **Tool-Schemas sind Public API** — einmal deployed gelten sie als Versprechen an Konsumenten
- **Keine Secrets in Tool-Descriptions** oder Schema-Feldern
- **Auth immer server-side** prüfen — nie dem Client vertrauen
- **Rate Limiting** schützt vor versehentlichem Over-Use durch agentic Workflows

---

## Weiterführende Quellen

- Offizielle MCP-Spec: https://spec.modelcontextprotocol.io
- Anthropic MCP-Docs: https://docs.anthropic.com/de/docs/agents-and-tools/mcp
- Remote MCP in Claude.ai: https://support.anthropic.com/integrations
