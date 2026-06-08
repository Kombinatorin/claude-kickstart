# GPT-Image-2 Quick Reference

**Produkt:** ChatGPT Images 2.0 (OpenAI Business-Account)
**API-Modell:** `gpt-image-2` (pinned: `gpt-image-2-2026-04-21`)
**Release:** 21. April 2026

---

## API Endpoints

| Endpoint | Zweck |
|---------|-------|
| `POST /v1/images/generations` | Text → Bild |
| `POST /v1/images/edits` | Bild bearbeiten (Maske oder Referenzbilder) |
| `POST /v1/responses` | Bild als Tool in einem Conversation-Turn |

---

## Parameter: `/v1/images/generations`

```ts
const result = await openai.images.generate({
  model: "gpt-image-2",
  prompt: "...",
  size: "1024x1024",     // flex: multiples of 16, max 3840, ratio ≤ 3:1
  quality: "medium",     // low | medium | high | auto
  output_format: "png",  // png | jpeg | webp
  n: 1,
  moderation: "auto",    // auto | low
});
// Response: result.data[0].b64_json (base64-encoded image)
```

### Gültige Größen für Instagram

| Format | Größe | Seitenverhältnis |
|--------|-------|-----------------|
| Square Feed | `1024x1024` | 1:1 |
| Portrait Feed (4:5) | `1024x1280` | 4:5 |
| Landscape | `1536x1024` | 3:2 |
| 4K Square | `2048x2048` | 1:1 |

Regeln: beide Werte vielfache von 16, max. Kante 3840px, Verhältnis ≤ 3:1.

### Qualität vs. Kosten

| Quality | 1024×1024 Kosten (ca.) | Einsatz |
|---------|------------------------|---------|
| `low` | ~$0.006 | Draft / Test |
| `medium` | ~$0.07 | Standard Instagram-Posts |
| `high` | ~$0.211 | Hero-Bilder, Kampagnenmaterial, Druck |
| `auto` | variiert | Lässt Modell entscheiden |

---

## Parameter: `/v1/images/edits`

```ts
const result = await openai.images.edit({
  model: "gpt-image-2",
  image: fs.createReadStream("input.png"),  // oder mehrere: [img1, img2]
  prompt: "Add our brand logo to the bottom-right corner",
  mask: fs.createReadStream("mask.png"),    // optional: Alpha-Kanal = editierbare Zone
  size: "1024x1024",
  quality: "medium",
});
```

**Wichtig:** mask muss gleiche Größe/Format wie input, muss Alpha-Kanal haben.
Bis zu 8 Referenzbilder pro Aufruf, je max. 50 MB (PNG/WEBP/JPG).

---

## Latenz-Hinweise

- `low` quality: ~5–15 Sekunden
- `medium` quality: ~15–45 Sekunden
- `high` quality: bis zu 2 Minuten
- Trigger.dev: `maxDuration` auf mindestens `180` setzen für `high`

---

## Branded Style Prompt (Template)

Halte einen wiederverwendbaren Prompt-Block pro Brand vor, der Farben, Typografie, Stil-Constraints und ein Markenzeichen festschreibt. Beispielstruktur:

```
[Bildtyp/Use-Case in 1 Satz].
Hintergrund: <Farbcode>. Primärtext: <Farbcode>. Akzentfarbe: <Farbcode>.
Typografie: <Schriftfamilie/Stilbeschreibung>.
Layout-Prinzip: <minimal / data-heavy / illustrativ / ...>.
Brand-Element: kleines "<Wordmark>" in <Position>.
Constraints: <z.B. neutral, keine Personen, keine Logos Dritter>.
Sprache der Beschriftungen: <Sprache>.
```

Tipp: Farb-Tokens aus dem CSS deines Projekts (z.B. `--brand-accent`) als Single Source of Truth in den Prompt einsetzen — so bleiben Bild und UI konsistent.

---

## Content-Formate (Beispiel-Tabelle)

Pflege pro Use-Case einen Eintrag mit Größe, Quality und Kosten. Beispiel:

| Format | Größe | Quality | Geschätzter Preis |
|--------|-------|---------|-------------------|
| Social Square | 1024x1024 | medium | ~$0.07 |
| Story / Reel | 1024x1280 | medium | ~$0.09 |
| Hero / Kampagne | 1024x1280 | high | ~$0.25 |

Budget-Cap empfohlen: feste Tages- oder Monats-Obergrenze (z.B. max. 10 Bilder/Tag) — verhindert versehentliche Kostenexplosion durch Agent-Loops.

---

## MCP-Server für Claude Code (Entwicklung / Prompt-Tests)

Verwende `mcp-image` (npm-Paket, OpenAI Provider) für direkte Bildgenerierung
während der Entwicklung — so kannst du Prompts testen, bevor sie in Trigger.dev-Tasks gehen.

### .mcp.json Eintrag

```json
"mcp-image": {
  "command": "npx",
  "args": ["-y", "mcp-image"],
  "env": {
    "IMAGE_PROVIDER": "openai",
    "OPENAI_API_KEY": "${OPENAI_API_KEY}",
    "IMAGE_OUTPUT_DIR": "${WORKSPACE_ROOT}/references/generated-images"
  }
}
```

Alternativ (mehr Kontrolle, Iterative Sessions): `Borys520/gpt-image-2-mcp` klonen + bauen.

---

## TypeScript SDK-Pattern (in Trigger.dev-Tasks)

```ts
import OpenAI from "openai";

function getOpenAI() {
  return new OpenAI({ apiKey: process.env.OPENAI_API_KEY });
}

// Bild generieren → base64 → Buffer
const response = await getOpenAI().images.generate({
  model: "gpt-image-2",
  prompt,
  size: "1024x1024",
  quality: "medium",
  output_format: "png",
  n: 1,
});
const b64 = response.data[0].b64_json!;
const buffer = Buffer.from(b64, "base64");
```

---

## Wichtige Einschränkungen

- Kein Streaming — Bild kommt als fertiges b64_json zurück
- Text-Rendering stark verbessert, aber bei sehr langen Sätzen noch fehlerhaft
- Character-Consistency: Wiederholbare Charaktere am besten mit Referenzbild via `/edits`
- Organisation muss bei OpenAI verifiziert sein für gpt-image-2 Zugang
- OpenAI SDK Version: mind. `openai@4.90+` für gpt-image-2 Support prüfen

---

## Links

- [OpenAI Image Generation Guide](https://developers.openai.com/api/docs/guides/image-generation)
- [gpt-image-2 Model Docs](https://developers.openai.com/api/docs/models/gpt-image-2)
- [mcp-image npm](https://www.npmjs.com/package/mcp-image)
- [gpt-image-2-mcp GitHub](https://github.com/Borys520/gpt-image-2-mcp)
