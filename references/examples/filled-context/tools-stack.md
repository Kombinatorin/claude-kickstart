# Tools & Stack

## Sprachen / Frameworks
- TypeScript + Next.js 15 (App Router) — Frontend & API-Routes
- Node.js 22 für Background-Worker

## Datenbank
Supabase (Postgres 16). 1 Projekt, ca. 30 Tabellen, ca. 8 GB Daten.

## Hosting / Deployment
- Frontend: Vercel (Hobby-Plan, reicht aktuell)
- Background Jobs: Trigger.dev (Cloud)
- Domain: invoicemate.de, DNS bei Hetzner

## Auth
Supabase Auth (Email + Magic Link). Kein OAuth, brauche keine Social Logins.

## Background Jobs / Queues
Trigger.dev für: PDF-Generierung, DATEV-Export, Erinnerungs-Mails, Stripe-Webhook-Verarbeitung.

## Mail / Notifications
Resend für Transactional Mail. ConvertKit für Newsletter.

## Zahlung
Stripe (Subscription + One-Off). Stripe Customer Portal für Self-Service-Verwaltung.

## Beobachtbarkeit (Logs, Metrics, Errors)
- Sentry für Errors (Free-Plan, reicht)
- Vercel Logs + Trigger.dev Logs reichen aktuell
- Kein dediziertes APM

## MCP-Server (in .mcp.json)
- `github` — Lese-Zugriff auf eigene Repos für Code-Fragen
- `trigger` — Run-Inspektion und Cost-Audits

## Editor / Terminal
VS Code + Claude Code Extension. Terminal: Ghostty. Shell: zsh.
