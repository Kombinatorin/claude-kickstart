# github-mcp-server

## Repo
https://github.com/github/github-mcp-server

## Was
Offizieller MCP-Server von GitHub Inc. — exposed die gesamte GitHub-API als typed MCP-Tools: Issues, PRs, Repos, Actions, Code-Search, Notifications, Discussions, Security-Advisories, etc.

## Stack
Go-Binary, MIT, v1.0.4 (Mai 2026). 29.8k Stars. Distributed via Docker (`ghcr.io/github/github-mcp-server`) oder Homebrew (`brew install github-mcp-server`). Lokale Installation auf macOS: `/opt/homebrew/bin/github-mcp-server`.

## Wann nutzen
- Claude Code / Claude Desktop sollen GitHub-Aktionen agent-native ausführen (statt `gh` CLI via Shell)
- Multi-Step-Workflows mit Tool-Discovery (Issue lesen → Kommentar → Label → PR-Verknüpfung)
- Strukturierte JSON-Outputs statt Shell-String-Parsing

## Wann NICHT nutzen
- Reine Shell-Automation in Bash-Scripts → `gh` CLI bleibt schneller
- Bulk-Operationen über Tausende Items → Custom-Script mit Pagination

## Auth
Personal Access Token via env var `GITHUB_PERSONAL_ACCESS_TOKEN`. Hosted-Variante mit OAuth nur mit aktivem GitHub-Copilot — bei uns **nicht aktiv**, daher self-hosted via Brew.

## Cross-References
- Deep-Dive: [../claude/github-mcp-server-guide.md](../claude/github-mcp-server-guide.md)
- Adoption-Workflow: [../sops/repo-adoption.md](../sops/repo-adoption.md) (Kategorie L1c)
- MCP-Konventionen: [../claude/mcp-notes.md](../claude/mcp-notes.md)
- Verwandte CLI: [github.md](github.md) (`gh` CLI)

## Status
L1c (Consumer MCP Server). Self-hosted via Homebrew, PAT-Auth, gewired in Claude Code `.mcp.json` und (optional) Claude Desktop. In-Product-Embedding für eigene Agenten ist möglich, aber separate Aktivierung nötig.
