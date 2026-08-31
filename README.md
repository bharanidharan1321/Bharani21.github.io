# ♻️ KabadiConnect

**Fair-price scrap selling for everyone — households, students, offices, small businesses, and informal collectors — connected to authorized recyclers.**
SIH26229 — Ministry of Mines | SIH 2026 Prototype

> "Turning Every Scrap into a Sustainable Future"

### The problem
Finding a better scrap price is difficult for ordinary people — they have to manually
search, call, compare, and negotiate with multiple local scrap dealers. Many simply sell
to the nearest dealer without knowing whether they received a fair price.

### The flow
User has scrap → select material + quantity → see estimated fair price → discover nearby
suitable recyclers → compare options → complete sale → get a digital receipt → earn Green Points.

KabadiConnect is open to **any scrap seller** — not just kabadiwalas — while still fulfilling
the official SIH brief of connecting informal collectors with authorized recyclers to support
formal, compliant recycling.

---

## Quick Start (Demo Mode — no setup required)

```bash
npm install
npm run dev
```

Open the printed local URL (usually `http://localhost:5173`).

That's it — **no Firebase keys, no API keys, no environment variables needed.**
The app runs entirely on a built-in demo data layer (localStorage-backed mock database)
that's pre-seeded with realistic collectors, recyclers, and transactions around Trichy, Tamil Nadu.

### Build for production
```bash
npm run build
npm run preview
```

---

## How to demo the app

From the landing screen, pick a role:

- **Collector** → mobile-first flow (login with any name + 10-digit number, no OTP)
- **Recycler** → desktop dashboard (auto-logs in as "GreenCycle Recycling")
- **Platform Admin** → desktop dashboard (auto-logs in as "Platform Admin")

### The core demo flow (collector)
Login → Home → **Sell Scrap** → pick **PCB** → set quantity to **2.5 kg** →
see fair price **₹700–₹800** → **Find Recycler** → select **GreenCycle Recycling** →
confirm → **QR digital receipt** appears → Green Points increase → visible instantly
in the Recycler dashboard's transaction table.

---

## Tech stack

| Layer | Choice |
|---|---|
| Frontend | React 19 + Vite + TypeScript |
| Styling | Tailwind CSS v4 (custom eco-tech design tokens) |
| Routing | React Router v7 |
| Maps | React-Leaflet + OpenStreetMap tiles (no API key) |
| Charts | Recharts |
| QR codes | `qrcode` |
| Icons | lucide-react |
| Data | Demo layer over `localStorage`, structured to mirror Firestore |

## Switching to real Firebase later

All data access goes through `src/services/store.ts`. Each exported function
(`getUsers`, `createTransaction`, `getTransactions`, etc.) is written so its
signature maps 1:1 onto a Firestore call. To go live:

1. `npm install firebase`
2. Add a `src/services/firebase.ts` with your project config (keep it out of
   version control / use `.env` + `import.meta.env`)
3. Replace each function body in `store.ts` with the equivalent
   `getDocs` / `addDoc` / `updateDoc` call against the collections described
   below — no other file needs to change.

### Firestore collection shapes (for reference)
```
users          { id, name, phone, role, language, greenPoints, createdAt }
recyclers      { id, name, address, latitude, longitude, phone, rating, verified, acceptedMaterials[] }
materials      { id, name, icon, pricePerKg, category }
transactions   { id, collectorId, recyclerId, materialId, materialName, quantity, price, status, timestamp, location, qrData }
rewards        { id, collectorId, points, reason, timestamp }
```

---

## Features implemented

**Collector (mobile-first PWA-style app):**
Splash → role select → login (English/Tamil toggle, OTP-free demo auth) → home
(green points, quick actions, impact card, nearby recyclers) → sell scrap
(material picker with icons) → quantity stepper → fair-price estimate →
Leaflet map + recycler list/filter by material → recycler details →
confirm transaction → **digital QR handover receipt** (downloadable) →
rewards (progress bar, tier rewards, points log) → transaction history.

**AI feature demos (non-blocking, simulated):**
- Voice scrap entry (simulated Tamil speech → material + quantity)
- Photo-based material recognition (simulated confidence score)
- Floating rule-based AI assistant answering real questions from live app
  data (prices, which recycler accepts what, how the flow works)

**Recycler dashboard (desktop):**
Overview stats, 7-day collection trend chart, material breakdown, recent
transactions table, full transaction management (accept/complete), collector
directory, material price reference, analytics (pie + trend charts),
compliance/audit-trail view, profile.

**Admin dashboard (desktop):**
Platform-wide stats, recycler verification management, material price
editor, full transaction log.

---

## Known limitations (by design, for a hackathon prototype)

- Data persists in the browser's `localStorage`, not a real shared database —
  multiple browsers/devices won't see each other's changes until wired to Firebase.
- Login is OTP-free / simplified for demo speed.
- Voice and photo recognition are simulated (fixed demo outputs) rather than
  calling a real speech/vision API — this was an explicit requirement so the
  app works with zero paid API keys.
- Recycler/Admin "login" is a one-click persona switch rather than per-account
  authentication (fine for a single-device demo; would need real auth for production).
- Geolocation uses a fixed demo coordinate (Trichy) rather than the browser's
  live GPS, so distances are consistent for every judge running the demo.
