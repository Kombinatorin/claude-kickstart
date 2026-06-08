---
name: Stripe API Reference
description: Vollständige Stripe API Referenz für SaaS-Subscription-Billing — Objekte, Webhooks, Patterns, Integrations-Beispiel
type: reference
---

# Stripe API Reference

> Stand: April 2026 | Basis: https://docs.stripe.com/api

---

## 1. Authentifizierung

```typescript
import Stripe from "stripe";
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);
```

- **Test-Keys:** `sk_test_...` / `pk_test_...`
- **Live-Keys:** `sk_live_...` / `pk_live_...`
- Base URL: `https://api.stripe.com`
- Alle Requests: JSON-Response, form-encoded Body

---

## 2. Kern-Objekte

### Customer
```typescript
const customer = await stripe.customers.create({
  email: "user@org.de",
  name: "Organisation GmbH",
  metadata: { organization_id: "uuid-hier" }, // ← Tipp: für Webhook-Routing immer Org-/User-ID anhängen
});
// customer.id = "cus_..."
```

**Key Fields:** `id`, `email`, `name`, `metadata`, `default_source`, `invoice_settings.default_payment_method`

---

### Price
```typescript
// Abrufen (bereits in Dashboard angelegt)
const price = await stripe.prices.retrieve("price_...");
```

**Key Fields:**
| Field | Wert |
|-------|------|
| `id` | `price_...` |
| `product` | `prod_...` |
| `unit_amount` | Betrag in Cent (z.B. 14900 = €149) |
| `currency` | `"eur"` |
| `recurring.interval` | `"month"` / `"year"` |
| `billing_scheme` | `"per_unit"` |
| `active` | `true` |
| `lookup_key` | optionaler statischer String |

---

### Checkout Session
```typescript
const session = await stripe.checkout.sessions.create({
  mode: "subscription",
  customer_creation: "if_required",          // oder customer: "cus_..." wenn bekannt
  line_items: [{ price: "price_...", quantity: 1 }],
  success_url: "https://app.example.com/settings?success=1",
  cancel_url: "https://app.example.com/pricing",
  metadata: { organization_id: orgId },       // ← für Webhook-Verarbeitung
  subscription_data: {
    metadata: { organization_id: orgId },
  },
});
// session.url → Redirect-URL für Browser
```

**Key Fields:** `id`, `url`, `mode`, `status`, `customer`, `subscription`, `metadata`, `payment_status`

**Status-Flow:** `open` → `complete` / `expired`

---

### Subscription
```typescript
const sub = await stripe.subscriptions.retrieve("sub_...");
await stripe.subscriptions.update("sub_...", {
  items: [{ id: sub.items.data[0].id, price: "price_new_..." }],
});
await stripe.subscriptions.cancel("sub_...");
```

**Subscription Statuses:**
| Status | Bedeutung |
|--------|-----------|
| `trialing` | Läuft in Trial |
| `active` | Aktiv & bezahlt |
| `incomplete` | Erste Zahlung ausstehend |
| `incomplete_expired` | Erste Zahlung nach 23h nicht erfolgt |
| `past_due` | Folgerechnung fehlgeschlagen |
| `canceled` | Gekündigt |
| `unpaid` | Keine weiteren Rechnungen |
| `paused` | Trial beendet, keine Zahlungsmethode |

**Key Fields:** `id`, `customer`, `status`, `current_period_start`, `current_period_end`, `trial_start`, `trial_end`, `cancel_at_period_end`, `items.data[0].price.id`, `metadata`

---

### Invoice
**Status:** `draft` → `open` → `paid` / `uncollectible` / `void`

**Key Fields:** `id`, `status`, `customer`, `subscription`, `amount_due`, `amount_paid`, `amount_remaining`, `hosted_invoice_url`, `billing_reason`

---

## 3. Webhook Events (typisch relevant)

### Must-Handle Events
| Event | Wann | Aktion |
|-------|------|--------|
| `checkout.session.completed` | Checkout erfolgreich | Plan in DB setzen |
| `invoice.paid` | Rechnung bezahlt | Plan aktiv halten |
| `invoice.payment_failed` | Zahlung fehlgeschlagen | User benachrichtigen |
| `customer.subscription.updated` | Plan geändert | Plan in DB aktualisieren |
| `customer.subscription.deleted` | Abo gekündigt | Plan auf `trial`/`free` downgraden |
| `customer.subscription.trial_will_end` | 3 Tage vor Trial-Ende | Upgrade-E-Mail senden |

### Webhook Setup
```typescript
// Signature verifizieren (IMMER machen!)
const event = stripe.webhooks.constructEvent(
  rawBody,           // Buffer, nicht geparster JSON
  req.headers["stripe-signature"],
  process.env.BILLING_WEBHOOK_SECRET  // whsec_...
);

switch (event.type) {
  case "checkout.session.completed": {
    const session = event.data.object as Stripe.Checkout.Session;
    const orgId = session.metadata?.organization_id;
    const subscriptionId = session.subscription as string;
    // → Org updaten: plan, stripe_customer_id, stripe_subscription_id
    break;
  }
  case "customer.subscription.deleted": {
    const sub = event.data.object as Stripe.Subscription;
    const orgId = sub.metadata?.organization_id;
    // → Plan auf "starter" downgraden oder Trial setzen
    break;
  }
  case "invoice.payment_failed": {
    const invoice = event.data.object as Stripe.Invoice;
    // → User per E-Mail benachrichtigen
    break;
  }
}

res.json({ received: true }); // immer 200 zurückgeben
```

**Wichtig:**
- Body MUSS als raw Buffer ankommen (nicht JSON.parse vorher!)
- Immer sofort 200 antworten, Logik async
- Idempotency: Event-IDs loggen, Duplikate ignorieren

---

## 4. Customer Portal

```typescript
const portalSession = await stripe.billingPortal.sessions.create({
  customer: "cus_...",
  return_url: "https://app.example.com/settings",
});
// portalSession.url → Redirect
```

Kunden können damit selbst: Kreditkarte ändern, Plan wechseln, Abo kündigen.

---

## 5. Plan-Mapping (Beispiel)

| Plan-Name | Stripe Price-ID | ENV-Variable |
|-----------|-----------------|--------------|
| Starter €149/Mo | `price_...` | `VITE_STRIPE_PRICE_STARTER` |
| Professional €490/Mo | `price_...` | `VITE_STRIPE_PRICE_PROFESSIONAL` |

**ENV-Variablen in `.env.local`:**
```
STRIPE_SECRET_KEY=sk_test_...
VITE_STRIPE_PRICE_STARTER=price_...
VITE_STRIPE_PRICE_PROFESSIONAL=price_...
BILLING_WEBHOOK_SECRET=whsec_...
VITE_APP_URL=http://localhost:5173
```

---

## 6. Webhook lokal testen

```bash
# Stripe CLI installieren
brew install stripe/stripe-cli/stripe

# Login
stripe login

# Lokalen Webhook forwarden
stripe listen --forward-to localhost:5173/api/billing/stripe/webhook

# Test-Events auslösen
stripe trigger checkout.session.completed
stripe trigger invoice.payment_failed
```

---

## 7. Beispiel-Webhook-Flow

```
Stripe Checkout abgeschlossen
  → checkout.session.completed
  → session.metadata.organization_id auslesen
  → organizations SET plan='professional', stripe_customer_id, stripe_subscription_id
  → trial_ends_at = NULL (Trial beendet)

Zahlung fehlgeschlagen
  → invoice.payment_failed
  → E-Mail via Resend an Org-Owner
  → Nach 3 Versuchen: plan='trial' setzen

Abo gekündigt
  → customer.subscription.deleted
  → plan='starter' (Downgrade, kein Datenverlust)
  → E-Mail: "Dein Abo läuft zum XX aus"

Trial endet bald
  → customer.subscription.trial_will_end (3 Tage vorher)
  → Upgrade-E-Mail senden
```

---

## 8. Fehler-Codes

| HTTP | Bedeutung |
|------|-----------|
| 400 | Bad Request — Parameter fehlen/falsch |
| 401 | Unauthorized — API-Key falsch |
| 402 | Payment Required — Zahlung abgelehnt |
| 404 | Not Found — Objekt nicht gefunden |
| 429 | Too Many Requests — Rate Limit |
| 500 | Server Error — Stripe-Problem |

Stripe-Fehler haben immer: `error.type`, `error.code`, `error.message`, `error.param`
