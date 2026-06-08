# GitHub

## Zwei Zugriffspfade

| Pfad | Wofür | Doc |
|------|-------|-----|
| `gh` CLI | Shell-Automation, Bash-Scripts, manuelle One-Shots | dieses File |
| `github-mcp-server` | Claude Code / Desktop / Agents — agent-native Tool-Discovery + JSON | [github-mcp-server.md](github-mcp-server.md) |

Beide koexistieren. Faustregel:
- **Im Terminal selbst tippen oder Script schreiben** → `gh`
- **Claude soll GitHub-Aktion ausführen** → MCP-Server

---

## `gh` CLI

Bereits installiert (`gh version 2.92.0`). Authentifiziert via `gh auth login`.

Häufige Operationen:

```bash
gh pr list --repo owner/repo
gh issue create --title "Bug" --body "..."
gh api repos/owner/repo/contents/README.md
gh run watch
gh release view v1.0.4 --repo owner/repo --json assets
```

Für Discovery von Release-Assets (z.B. um Binary-Filenames zu finden) ist `gh release view --json assets --jq '.assets[].name'` zuverlässig.

---

## Repository-Workflow (Vorschlags-Konventionen)

- Branches: `feature/`-/`fix/`-Präfix bei Bedarf; bei Solo-Arbeit auch direct-to-main möglich
- Commits: präzise, keine generischen „update" / „fix"
- PRs: nur wenn Review sinnvoll; sonst direct merge
- CI: Deploy-Trigger bei Push (z.B. Vercel, Trigger.dev, eigene VPS) — Setup hängt vom Stack ab

---

## Risks

- PAT-Verwaltung: bei github-mcp-server **niemals** Token in `.mcp.json` hardcoden — nur env-var-Referenz
- `gh auth login` legt Token unter `~/.config/gh/hosts.yml` ab — Datei schützen
- Beide Tools können destruktiv sein (force-push, branch delete) — für KI-Aufrufe `--read-only`-Variante des MCP-Servers nutzen

---

## Cross-References

- MCP-Server-Adoption: [../sops/repo-adoption.md](../sops/repo-adoption.md) (L1c)
- MCP-Grundlagen: [../claude/mcp-foundations.md](../claude/mcp-foundations.md)
- Adoption-Decision: `/decisions/log.md` (Eintrag 2026-05-14)
