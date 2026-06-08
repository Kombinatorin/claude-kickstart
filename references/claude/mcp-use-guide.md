# mcp-use — Deep Dive

Quelle: https://github.com/mcp-use/mcp-use (MIT, 10k Stars, aktiv)

Dieses Dokument ist die Implementierungs-Referenz für mcp-use. Operative Konventionen (Deploy, Auth, Failure-Triage) stehen separat in [../sops/mcp-use.md](../sops/mcp-use.md). MCP-Grundlagen in [mcp-notes.md](mcp-notes.md).

---

## Was es löst

Schmerz beim Bauen eigener MCP-Server:
- Viel Boilerplate für JSON-RPC, Transport, Tool-Discovery
- Kein eingebauter Inspector für lokales Debugging
- Auth/Subscription-Gating muss von Hand gestrickt werden
- Hot-Reload für Tool-Definitions fehlt

mcp-use liefert: SDK (TS + Python), Web-Inspector unter `/inspector`, CLI (`@mcp-use/cli`) für Deployment, Widget-System für interaktive UI in Resources.

---

## Core-Konzepte

1. **MCPServer** — eigene Tools/Resources/Prompts deklarieren, lokal/standalone hosten
2. **MCPClient** — externe MCP-Server konsumieren (Alternative zu Custom-MCP-Clients)
3. **MCPAgent** — LLM-Agent, der Tools direkt aufruft (mit oder ohne LangChain)
4. **Inspector** — Web-UI für Tool-Discovery, Live-Test, Schema-Validation (`localhost:port/inspector`)
5. **Widgets** — React-Komponenten in `resources/`, auto-discovered, hot-reload im Dev

---

## Install

### TypeScript / Node

Scaffold neues Projekt:

```bash
npx create-mcp-use-app@latest
```

Oder in bestehendes Projekt:

```bash
npm install mcp-use zod
```

Inspector standalone (falls Server nicht selbst hostet):

```bash
npx @mcp-use/inspector --url http://localhost:3000/mcp
```

### Python

```bash
pip install mcp-use
```

Benötigt zusätzlich `mcp` (Python-MCP-Types) und `pydantic` für Tool-Schemas.

---

## Minimal Server (TypeScript)

```ts
import { MCPServer, text } from "mcp-use/server";
import { z } from "zod";

const server = new MCPServer({
  name: "my-server",
  version: "1.0.0",
});

server.tool({
  name: "get_weather",
  description: "Get weather for a city",
  schema: z.object({ city: z.string() }),
}, async ({ city }) => {
  return text(`Temperature: 72°F, Condition: sunny, City: ${city}`);
});

await server.listen(3000);
// Inspector unter http://localhost:3000/inspector
```

## Minimal Server (Python)

```python
from typing import Annotated
from mcp.types import ToolAnnotations
from pydantic import Field
from mcp_use import MCPServer

server = MCPServer(name="Weather Server", version="1.0.0")

@server.tool(
    name="get_weather",
    description="Get current weather information for a location",
    annotations=ToolAnnotations(readOnlyHint=True, openWorldHint=True),
)
async def get_weather(
    city: Annotated[str, Field(description="City name")],
) -> str:
    return f"Temperature: 72°F, Condition: sunny, City: {city}"

server.run(transport="streamable-http", port=8000)
# Inspector unter http://localhost:8000/inspector
```

---

## Inspector

- **Auto-included** beim `server.listen(port)` / `server.run(port=...)`: `http://localhost:<port>/inspector`
- **Standalone CLI**: `npx @mcp-use/inspector --url http://localhost:3000/mcp`
- **Online (hosted by mcp-use)**: https://inspector.mcp-use.com

→ Für unsere Use-Cases: **niemals den Inspector öffentlich exposen** (Tool-Liste leakt Business-Logik). Local dev only oder hinter Auth.

---

## Deployment

mcp-use CLI:

```bash
npx @mcp-use/cli login
npx @mcp-use/cli deploy
```

Deployt auf Manufact MCP Cloud (proprietär). Wenn du Vendor-Lock-in vermeiden willst, hoste selbst — mcp-use Server läuft als normaler Node/Python-Prozess und ist auf jedem bestehenden Stack deploybar (Vercel, Hostinger, Fly.io, eigenes VPS, etc.).

---

## LangChain-Bridge

mcp-use kann Tools eines MCP-Servers in LangChain-Agents injizieren. Beispiel aus den offiziellen Docs verwendet `@langchain/openai` bzw. `langchain-openai`. Relevant, wenn du LangChain-Pipelines baust.

Direkt-Variante ohne LangChain: `MCPAgent` aus mcp-use selbst.

---

## Telemetry — immer ausschalten

mcp-use sendet by default **anonyme Telemetrie an PostHog (EU)** + Scarf — inkl. Agent-Query-/Response-Inhalten und Server-Identifiers. Für jeden Use-Case mit sensitiven oder personenbezogenen Anfragen ist das nicht akzeptabel — Opt-out machen.

**Opt-out (Python + TS gleich):**

```bash
export MCP_USE_ANONYMIZED_TELEMETRY=false
```

In package.json-Scripts immer prepended setzen:

```json
"scripts": {
  "dev": "MCP_USE_ANONYMIZED_TELEMETRY=false tsx src/index.ts"
}
```

Verifikation im Boot-Log: `DEBUG: Telemetry disabled` (statt `INFO: Anonymized telemetry enabled…`).

---

## Gotchas (validiert im Spike 2026-05-14)

- **Tool-Property heißt `schema:`** — NICHT `inputSchema:`. SDK ignoriert `inputSchema:` stillschweigend, registriert leeres Schema, strippt alle Arguments. Verifikation: `tools/list` → `properties` muss deine Felder zeigen.
- **Middleware (`server.use("mcp:tools/call", …)`) sieht weder Tool-Name noch `_meta` noch HTTP-Header.** `ctx.params` enthält nur die Tool-Arguments, trotz gegenteiliger Type-Definition. Custom-Auth via Middleware ist nicht praktikabel — für Production OAuth-Provider verwenden, für Tests Tier-Check im Tool-Handler.
- **Auth-Pattern für Production:** Built-in OAuth-Provider (`oauthSupabaseProvider`, `oauthClerkProvider`, `oauthAuth0Provider` etc.) — Auth-Info landet in `ctx.auth`, von Middleware UND Handlern lesbar. NICHT Custom-Subscription-Keys im `_meta` oder Header verstecken.
- **Transport-Wahl**: Python-Beispiel nutzt `streamable-http`; TS-Beispiel implizit HTTP. Für Claude-Desktop muss `stdio` möglich sein.
- **Versionen**: README spezifiziert kein Node/Python-Minimum. Spike verifizierte Node 25 mit `zod@4.4.3` (mcp-use peerDep `zod: ^4.0.0`).
- **Manufact Lock-in**: CLI-Deploy zielt auf hosted Plattform. Self-Hosting funktioniert, ist aber nicht der Default-Workflow.
- **Telemetry**: Default-on; opt-out via `MCP_USE_ANONYMIZED_TELEMETRY=false` (siehe Sektion oben).
- **Widget-System (React)**: hübsch, aber zusätzliche Build-Pipeline. Separate Evaluation, falls relevant.

---

## Vergleich gegen Custom-MCP

Vorteile mcp-use:
- Eingebauter Inspector spart Stunden bei Tool-Schema-Debugging
- `zod`/`pydantic`-Schemas → automatische Validation
- Konsistente Struktur über Module hinweg

Nachteile / Risiken:
- Zusätzliche Abhängigkeit, ggf. Lock-in bei Major-Updates
- Manufact-Hosting eingebaute „Versuchung" zur Cloud-Verlagerung
- Subscription-Gating selbst lösen

---

## Cross-References

- Repo-Adoption-Workflow: [../sops/repo-adoption.md](../sops/repo-adoption.md)
- Tool-Stub: [../tools/mcp-use.md](../tools/mcp-use.md)
- Operative SOP: [../sops/mcp-use.md](../sops/mcp-use.md)
- MCP-Grundlagen: [mcp-notes.md](mcp-notes.md)
- Spike-Evaluation: `projects/active/spikes/mcp-use-eval/`
