# Gash Investments

Marketing site for **Gash Investments** — a personal financial services and algorithmic trading brand offering custom trading bots, financial budgets and forecasts, proprietary trading indicators, managed account services, and structured trading education.

Single-file static site. No build step, no dependencies, no framework.

**Live:** https://gashinvestments.com <!-- update once your domain is connected -->

---

## Contents

- [Stack](#stack)
- [Running locally](#running-locally)
- [Deploying](#deploying)
- [Before you go live](#before-you-go-live)
- [Wiring up the contact form](#wiring-up-the-contact-form)
- [Analytics](#analytics)
- [Customising](#customising)
- [Accessibility and performance notes](#accessibility-and-performance-notes)
- [Licence](#licence)

---

## Stack

| Layer | Choice |
|---|---|
| Markup | Hand-written HTML5 |
| Styling | Vanilla CSS with custom properties (design tokens in `:root`) |
| Scripting | Vanilla JavaScript, no libraries |
| Charts | Native Canvas 2D — candlesticks, EMAs, indicator overlays, particle field |
| Fonts | Space Grotesk, JetBrains Mono, Inter (Google Fonts) |
| Build | None. `index.html` is the whole site. |

Everything ships in one file so it can be hosted anywhere, edited without tooling, and loaded in a single request.

## Running locally

Open `index.html` in a browser. That's it.

If you'd rather serve it over HTTP (closer to production behaviour):

```bash
# Python
python3 -m http.server 8000

# or Node
npx serve .
```

Then visit `http://localhost:8000`.

## Deploying

Any static host works. Options, easiest first:

**GitHub Pages** — `Settings → Pages → Deploy from a branch → main / (root)`. Live at `https://<username>.github.io/<repo>`.

**Netlify** — drag the folder onto [app.netlify.com/drop](https://app.netlify.com/drop), or connect this repo for auto-deploy on push.

**Vercel** — import the repo, no configuration needed.

### Custom domain on GitHub Pages

Add the domain under `Settings → Pages`, then set these DNS records at your registrar:

| Type | Name | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `<username>.github.io` |

Propagation takes 10–30 minutes. Tick **Enforce HTTPS** once the certificate is issued.

---

## Before you go live

The site ships with deliberate placeholders. Search for these and replace them.

### Must fix

- [ ] **Stats row** — currently renders `—` with `← add real figure` labels. Put in true numbers, however modest. Search `data-to="0"`.
- [ ] **Testimonials** — three placeholder quotes behind a visible amber warning block. Replace with real, permissioned client quotes, then delete the `.placeholder-flag` div. Fabricated testimonials on a financial services site are a legal and reputational risk.
- [ ] **Regulatory status FAQ** — the answer is intentionally unwritten. Discretionary management of third-party funds is a licensed activity in most jurisdictions (CMA in Kenya, FCA in the UK). Get a straight answer from a local financial services lawyer, then state your actual position accurately.
- [ ] **Payments and refunds FAQ** — also unwritten. Set this policy deliberately.
- [ ] **Contact details** — email, WhatsApp number, hours, location.
- [ ] **Social links** — five `<a href="#" class="social">` in the footer.
- [ ] **Legal pages** — Privacy Policy, Terms, Risk Disclosure and Cookie Policy are `href="#"` stubs. You need real ones before running paid ads; ad platforms reject sites without them. [Termly](https://termly.io) or [iubenda](https://iubenda.com) will generate them cheaply.

### Should fix

- [ ] **Canonical URL and OG URLs** — currently hardcoded to `https://gashinvestments.com/`.
- [ ] **`og-image.png`** — referenced but not included. Make a 1200×630 image and drop it in the repo root, or link previews will render blank on WhatsApp and LinkedIn.
- [ ] **Favicon** — currently an inline SVG placeholder with a "G". Replace with a real icon.
- [ ] **Pricing** — the class prices ($149 / $349 / $699) and profit-share percentages are illustrative. Confirm they're what you actually charge.

### Leave alone

The **risk warning** in the footer. Keep it, and keep it visible. It is doing real work.

---

## Wiring up the contact form

The form is front-end only — it validates and shows a success state but sends nothing. Pick one:

**Formspree** — no backend needed:

```html
<form id="ctForm" action="https://formspree.io/f/YOUR_ID" method="POST">
```

Then remove the `e.preventDefault()` in the submit handler, and give each input a `name` attribute (`name="firstName"` etc.) so the values are actually submitted.

**Netlify Forms** — if hosting on Netlify, add `netlify` to the form tag and `name` attributes to inputs. Submissions appear in your Netlify dashboard.

**Google Forms** — crude but free. Point the action at a prefilled form endpoint.

Whichever you choose, confirm the consent checkbox value is captured and stored — you'll need it as a record of consent under the Kenya Data Protection Act and UK GDPR if you're contacting people commercially.

## Analytics

A Google Analytics 4 block sits commented out in `<head>`. Uncomment it and swap `G-XXXXXXXXXX` for your measurement ID.

If you add GA, Meta Pixel or any other tracker, you need a cookie consent banner and a cookie policy in most jurisdictions. Don't skip that step and then run ads.

## Customising

### Colours

All colours are CSS custom properties at the top of the `<style>` block:

```css
:root {
  --mint:   #00e6b8;  /* primary accent */
  --azure:  #2d9cff;  /* secondary */
  --amber:  #ffb020;  /* warnings, EMA 21 */
  --rose:   #ff4d6d;  /* bearish, errors */
  --violet: #a06bff;  /* volatility bands */
  --bg:     #080b10;  /* page background */
  --ink:    #eef3f8;  /* body text */
}
```

Change `--mint` and the whole site rebrands.

### Sections

Each section is a top-level `<section>` with an `id` matching its nav anchor: `#hero`, `#services`, `#bots`, `#indicators`, `#calculator`, `#classes`, `#accounts`, `#results`, `#faq`, `#contact`. Delete a section and remove its nav links from both `.nav-desktop` and `.drawer`.

### The charts

Three independent canvas modules, each an IIFE at the bottom of the file:

- `candles()` — hero chart. Randomly seeded OHLC data that ticks live. Not a real feed. If you want live prices you'll need a market data API and an API key, which means a backend or a proxy — don't put a key in client-side code.
- `indicators()` — the four-way overlay demo. Add a layer by extending the `notes` object and adding a branch in `draw()`.
- `particles()` — background node field. Density scales with viewport width.

### The calculator

`calc()` is pure arithmetic, no network calls, nothing stored. The break-even win rate is `1 / (1 + R:R)`, and the loss-streak figure solves for a 20% drawdown at the chosen risk per trade. If you change the maths, sanity-check it against a broker calculator before publishing.

## Accessibility and performance notes

- Single HTTP request for markup, styles and scripts. Only external requests are Google Fonts.
- `prefers-reduced-motion: reduce` is honoured — all animation stops, reveals render immediately, charts draw once and hold.
- Hamburger menu has `aria-expanded`; carousel dots and arrows have `aria-label`s.
- Canvas charts are decorative. Any information a visitor genuinely needs is also present as text.
- Colour contrast on `--ink-3` against `--bg` is deliberately low for de-emphasised metadata. Don't use it for anything a visitor must read.

## Licence

**All rights reserved.**

The design, copy, branding and code in this repository are the property of Gash Investments. The repository is public so the site can be served via GitHub Pages, not as an invitation to reuse. No licence is granted for commercial use, redistribution or derivative works.

Copyright applies automatically with or without a `LICENSE` file — omitting one is the intentional choice here, since this is a commercial brand asset rather than an open source project.

---

## Disclaimer

Trading foreign exchange, commodities, indices and derivatives carries a high level of risk and can result in the loss of all invested capital. Nothing in this repository or on the site it produces constitutes financial advice or a recommendation to trade any instrument. Backtested and hypothetical results are not reliable indicators of future performance.# gash-investments
