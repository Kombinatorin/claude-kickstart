# Aktive Projekte

Hier liegen alle laufenden Projekte — pro Projekt ein Unterordner.

## Neues Projekt anlegen

**Variante A: via Agent**
```
@project-bootstrapper Lege ein neues Projekt "mein-projekt" an
```

**Variante B: manuell**
```bash
cp -r projects/_templates/project-template projects/active/mein-projekt
```

## Konvention

- Slug-style Namen (`kleinbuchstaben-mit-bindestrich`)
- Pro Projekt: README, STRATEGY.md, IMPL-NOTES.md, VALIDATION.md (entsteht im Multi-Agent-Workflow)
- Brainstorms zum Projekt bleiben in `operations/brainstorms/` — nur die destillierten Outputs landen hier

## Wenn abgeschlossen

Verschiebe den Ordner nach `projects/archive/<jahr>/`.
