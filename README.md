# Card Advisor

A personal tool that answers one question: which card should I actually use right now?

I have six credit cards. Each one is better than the others in specific situations — but keeping that matrix in my head while standing at a checkout counter isn't realistic. So I built this instead.

**[Live demo →](https://prashantapurkayastha.github.io/card-advisor)**

![Card Advisor Screenshot](screenshot.png)

---

## What it does

Type a spend — "ordering Swiggy", "booking flights to Vietnam", "shopping on Myntra" — and the app tells you which card to use and why. The recommendation includes the effective cashback/reward rate, a plain-English reason, and a runner-up in case the first card is maxed for the month.

Two modes:

- **Rule-based** — instant, offline, no API calls. A decision engine built from researched card data.
- **AI-powered** — routes to a Gemini 2.5 Flash proxy that reasons across all six cards and returns a structured recommendation with a pro tip.

The six cards in my wallet:

| Card | Network | Best For |
|------|---------|----------|
| SBI SimplySave | RuPay | Dining, movies, grocery |
| Yes Bank Uni Gold | Mastercard | Forex, flights, hotels |
| HDFC Swiggy | Mastercard | Swiggy, online shopping |
| HDFC Regalia Gold | Visa/MC | All-around, Myntra/Nykaa |
| ICICI Sapphiro Amex | Amex | BookMyShow BOGO, lounge |
| ICICI Sapphiro MC | Mastercard | Stack with Amex for 4 free movies/month |

---

## Architecture

```
Browser (GitHub Pages)
    │
    ├── Rule-based mode: pure JS, no network call
    │
    └── AI mode: POST /ask
            │
            ▼
    Vercel Proxy (Node.js)
            │
            ▼
    Gemini 2.5 Flash API
```

The frontend is a single HTML file — no framework, no build step, no node_modules. The Gemini API key lives in the proxy's environment variables and never touches the browser.

Proxy repo: [card-advisor-proxy →](https://github.com/prashantapurkayastha/card-advisor-proxy)

---

## Running locally

No build step needed. Just open `index.html` in a browser.

For AI mode to work locally, you'll need the proxy running:

```bash
# Clone the proxy repo
git clone https://github.com/prashantapurkayastha/card-advisor-proxy
cd card-advisor-proxy
npm install

# Add your Gemini API key
echo "GEMINI_API_KEY=your_key_here" > .env

npm start
```

Then update `PROXY_URL` in `index.html` to `http://localhost:3000` and open the file.

---

## Design decisions

**Why a single HTML file?** The tool is personal — I use it on my phone, sometimes offline. A single file means I can save it locally or share it as an attachment without any hosting dependency. The rule-based mode works entirely without a network connection.

**Why a proxy instead of calling Gemini directly?** Putting an API key in client-side JS means it's visible to anyone who opens DevTools. The proxy costs nothing to run (Vercel's free tier handles this workload without breaking a sweat) and keeps the key server-side permanently.

**Why two modes?** The rule-based engine is fast and deterministic — good for quick lookups. The AI mode handles edge cases and ambiguous queries better, and adds context I didn't anticipate when writing the rules.

---

## Tech stack

- Vanilla HTML/CSS/JS — no framework
- SVG card graphics (custom, hand-coded)
- Gemini 2.5 Flash via REST API
- Hosted on GitHub Pages
