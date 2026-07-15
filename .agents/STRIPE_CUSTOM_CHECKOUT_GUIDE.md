# Stripe Custom Checkout (`ui_mode: 'custom'`) — Agent Integration Guide

**What this is.** A drop-in reference for any project using Stripe **Custom Checkout** (`ui_mode: 'custom'` — Stripe's own UI elements, themed and embedded in *your* page; not hosted/redirect Checkout, not raw Payment Element + PaymentIntents). **Read this before writing or debugging a Custom Checkout flow.** It exists because this exact integration has been independently re-derived — and broken multiple times — across projects, always for the same two reasons. It hands you the verified-correct flow and a copy-paste browser probe so you confirm the live bundle yourself instead of trusting docs that *will* mislead you.

---

## Why you can't trust the docs (read this first — it's the whole point)

Stripe ships several integrations that share vocabulary ("Checkout," "Elements," "Payment Element"): hosted Checkout, embedded Checkout, Elements + PaymentIntents, and Custom Checkout. Public docs **and LLM training** blend them, so a search for "Stripe custom checkout elements" returns confidently-wrong answers for *this* flow. Two specific traps cause ~all the pain:

1. **Wrong init method.** The bundle at `https://js.stripe.com/basil/stripe.js` exposes **both** `stripe.initCheckout(...)` **and** `stripe.initCheckoutElementsSdk(...)`. Docs/LLMs frequently push `initCheckoutElementsSdk`. For the Custom Checkout + `fetchClientSecret` flow the correct entry is **`stripe.initCheckout({ fetchClientSecret })`**. Because *both* exist, "the method is missing" is never your error — you'll silently use the wrong one and nothing behaves as the docs claim.

2. **The double-writer bug (the one that costs days).** Stripe's mounted elements (`createContactDetailsElement`, `createShippingAddressElement`, `createBillingAddressElement`, `createPaymentElement`) **collect and sync their own data to the session automatically.** If you *also* call `checkout.updateShippingAddress()` / `updateBillingAddress()` (because a doc told you to), you now have **two writers for one field** → `IntegrationError: …the Payment Element may also be collecting this field` → **`confirm()` is permanently blocked.** The mounted elements own collection. **Never put an `update*` call on top of a mounted element.**

**The one durable rule: probe the live bundle before you build, and lead with the probe-proven calls.** "Basil" is a moving target; exact option names drift. The *architecture* — `initCheckout({ fetchClientSecret })` + `confirm()`, elements own collection, no double-writers — is stable. Re-verify the *exact options* per project with the 5-minute probe below.

---

## The verified-correct flow

### Server — create the session, return the clientSecret

```ts
const session = await stripe.checkout.sessions.create({
  ui_mode: 'custom',
  mode: 'payment',
  line_items,
  allow_promotion_codes: true,
  shipping_address_collection: { allowed_countries: ['US'] }, // your countries
  // ALWAYS provide a shipping option (even $0). Without one the total never resolves
  // and session.canConfirm stays false forever.
  shipping_options: [{
    shipping_rate_data: {
      type: 'fixed_amount',
      fixed_amount: { amount: 0, currency: 'usd' },
      display_name: 'Free shipping',
    },
  }],
  // Collect phone SERVER-SIDE — the address element REJECTS a `fields` option (see gotchas).
  phone_number_collection: { enabled: true },
  // OMIT customer_creation unless you've verified it populates session.customer under
  // ui_mode:'custom' (it can 500 the create or no-op). Null-guard stripe_customer_id downstream.
  return_url: `${baseUrl}/complete?session_id={CHECKOUT_SESSION_ID}`,
});
// respond with { clientSecret: session.client_secret }
```

- **Never pass `payment_method_types`** — let Stripe's dynamic payment methods decide (configure in the Dashboard).
- `return_url` must contain the literal `{CHECKOUT_SESSION_ID}` template. With Vercel `cleanUrls`, drop the `.html`.

### Client — init, mount, listen (read-only), confirm

```js
const checkout = await stripe.initCheckout({
  fetchClientSecret: async () => clientSecret,  // NOT `clientSecret:` ; NOT initCheckoutElementsSdk
  elementsOptions: {
    appearance: { theme: 'stripe', variables: { colorPrimary: '#000000' } },
    syncAddressCheckbox: 'billing', // renders ONE default-checked "billing same as shipping"
  },
  // NO defaultValues — initCheckout REJECTS it (see gotchas). Field prefill is not supported here.
});

checkout.createContactDetailsElement().mount('#contact');
checkout.createShippingAddressElement({ display: { name: 'split' } }).mount('#shipping'); // no `fields`
checkout.createBillingAddressElement().mount('#billing');
checkout.createPaymentElement({ fields: { billingDetails: 'never' } }).mount('#payment'); // billing comes from the billing element

// READ-ONLY listener: gate the pay button + paint totals. NEVER write back to the session here.
checkout.on('change', (session) => {
  payBtn.disabled = !session.canConfirm;
  // session.total?.total?.amount, session.shippingOption?.total?.amount,
  // session.shippingAddress?.address?.country, etc.
});

payBtn.addEventListener('click', async () => {
  const r = await checkout.confirm();                 // redirects to return_url on success
  if (r && r.type === 'error') show(r.error.message); // declines / validation errors land here
});
```

**There are zero `update*` calls. That is the point.**

---

## Gotchas (observed live — with the exact error strings)

| If you…                                                             | Reality                                                                                           | Symptom / error                                                                                                      |
| ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| use `initCheckoutElementsSdk`                                       | wrong entry for this flow                                                                         | silent wrong-API; nothing works as the docs say                                                                      |
| call `update*` over a mounted element                               | forbidden (double-writer)                                                                         | `IntegrationError: …may also be collecting this field`; `confirm()` blocked                                          |
| pass `defaultValues` to `initCheckout`                              | rejected                                                                                          | `IntegrationError: options.defaultValues is not an accepted parameter`                                               |
| pass `fields` to `createShippingAddressElement`                     | rejected                                                                                          | `IntegrationError: options.fields is not an accepted parameter`                                                      |
| need to collect phone                                               | use server `phone_number_collection`, NOT element `fields`                                        | —                                                                                                                    |
| call `checkout.applyDiscount(...)`                                  | doesn't exist                                                                                     | use `checkout.applyPromotionCode(code)`                                                                              |
| omit `shipping_options`                                             | total never resolves                                                                              | `session.canConfirm` stuck `false`; "$NaN" totals                                                                    |
| let the Payment Element collect billing AND mount a billing element | duplication / double-writer                                                                       | use `createPaymentElement({ fields:{ billingDetails:'never' } })` + separate billing element + `syncAddressCheckbox` |
| set `customer_creation:'always'` under `ui_mode:'custom'`           | can 500 the create or not populate `session.customer`                                             | omit it; null-guard `stripe_customer_id`; derive your customer by email                                              |
| prefill the email                                                   | unsupported via `defaultValues`; `updateEmail()` over the mounted contact element = double-writer | leave the contact element empty; capture email elsewhere                                                             |

`name: 'split'` (First/Last) **is** accepted via `display: { name: 'split' }` on the shipping element. Method surface (enumerate with `Object.keys(checkout)` — they're own-properties, not on the prototype): `createContactDetailsElement`, `createEmailElement`, `createShippingAddressElement`, `createBillingAddressElement`, `createPaymentElement`, `createExpressCheckoutElement`, `confirm`, `applyPromotionCode`, `removePromotionCode`, `on`, `session`, plus the `update*` family (which you deliberately don't use for addresses).

---

## Probe-first protocol (run on the real preview, before building)

Confirm the surface yourself in ~5 minutes. **Must run on a real deployed preview URL** — not `vercel dev` / localhost; the loaded bundle (and any deploy-protection/SSO) only behave correctly on a real URL. If the preview has deploy protection, use a browser already authenticated to it.

```js
// 1) Init method check (no session needed)
const k = window._stripePublishableKey;          // or your publishable key
typeof Stripe(k).initCheckout;                    // 'function' — USE THIS
typeof Stripe(k).initCheckoutElementsSdk;         // also 'function' — DON'T

// 2) Get a clientSecret however your app does (call your create endpoint), then init + enumerate
const c = await Stripe(k).initCheckout({ fetchClientSecret: async () => cs, elementsOptions: { appearance: { theme: 'stripe' } } });
Object.keys(c).sort();          // real method surface
Object.keys(c.session()).sort(); // session shape — look for canConfirm, total, email, billingAddress, lineItems

// 3) Option-acceptance (each rejected option throws SYNCHRONOUSLY — wrap in try/catch)
//    e.g. a fresh init with defaultValues should throw; createShippingAddressElement({fields:...}) should throw;
//    createPaymentElement({ fields:{ billingDetails:'never' } }) and createShippingAddressElement({ display:{ name:'split' } }) should be ok.

// 4) Composition mount test: inject temp divs, mount contact+shipping+billing+payment,
//    wait ~3s, confirm NO IntegrationError in the console and that iframes render.
//    That proves the no-double-writer composition for your project's bundle version.
```

Record the answers — they fill your build doc's "verified contract" so the building agent never guesses. (The full confirm() round-trip and typed-address auto-sync need a test card + filled form — validate those in your end-to-end test pass, not the probe.)

---

## Deployment notes (Vercel)

- `api/*.ts` must compile to **CommonJS** (set `module`/`target` in tsconfig). ES2020 output crashes the deployed runtime and only surfaces on a real preview URL — `vercel dev` masks it.
- Test on the **preview URL**, never localhost.
- With `cleanUrls: true`, `return_url` and redirects drop `.html`.
- Buyer order confirmation can be Stripe's native branded receipt (Dashboard → Settings → Emails + Branding) — no custom buyer email needed for v1.

## Post-mortem (why this guide exists)

On one project this exact flow was rebuilt **five times** before the root cause was isolated: manual `update*` bridges fighting the self-syncing elements, plus chasing `initCheckoutElementsSdk` from the docs. The fix wasn't new code — it was *deleting* the bridges and leading with `initCheckout({ fetchClientSecret })` + `confirm()`. The probe above would have found it in one session. **Probe first; trust the bundle over the docs.**
