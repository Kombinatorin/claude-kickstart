# Brainstorms

Output-Verzeichnis für `/grill-me` und `/onboard`.

## Was hier landet

Jede `/grill-me`-Session legt eine Datei nach dem Schema `YYYY-MM-DD-<topic-slug>.md` an. Inhalt: Frage-Antwort-Verlauf, Flags, offene Punkte.

## Regeln

- **Nie löschen, nie verschieben.** Brainstorm-Files sind das Roh-Audit deines Denkens — auch nach 2 Jahren wertvoll.
- **Promotion statt Verschieben:** Wenn aus einem Brainstorm Entscheidungen, Tasks oder Projekte werden, kopiere die relevanten Stellen nach `decisions/log.md`, `operations/inbox.md` oder `projects/active/<x>/`.
- **Versionierung:** `.gitignore` hält Brainstorm-`.md`-Dateien standardmäßig **aus** dem Repo (privat). Wenn du sie versionieren willst, passe `.gitignore` an.

## Detail-SOPs

- `/grill-me` — siehe [../../references/sops/grill-me.md](../../references/sops/grill-me.md)
- `/onboard` — siehe [../../references/sops/onboard.md](../../references/sops/onboard.md)
