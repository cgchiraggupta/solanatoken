# 🐦 Dexploy — Tweet to Token on Stellar

> Paste a tweet. Get a token. In under 5 seconds.

**Dexploy** is a tweet-to-token platform built on the [Stellar](https://stellar.org) blockchain. Drop any tweet URL, and Dexploy automatically extracts the content, generates token metadata, pixel-art transforms the image, and deploys a live Stellar asset — all in one click.

Built for the **Stellar Hackathon 2025**.

---

## ✨ What It Does

| Step | What Happens |
|---|---|
| 1. Paste tweet URL | Dexploy scrapes tweet text + image via Twitter API v2 |
| 2. Auto-generate metadata | Token name, symbol, and supply are derived from the tweet |
| 3. Pixel-art transform | The tweet image is converted to a pixel-art logo and pinned to IPFS |
| 4. Deploy on Stellar | A native Stellar asset is issued via Horizon API in real time |
| 5. Share it | Get an instant share card — tweet your token to the world |

---

## 🛠️ Tech Stack

| Layer | Tech |
|---|---|
| Frontend | Next.js 14 + Tailwind CSS |
| Wallet | Freighter (Stellar wallet browser extension) |
| Backend | FastAPI (Python) |
| Tweet parsing | Twitter API v2 |
| Token deployment | `stellar-sdk` (Python) via Horizon API |
| Smart contract (optional) | Soroban / Rust (SAC wrapper) |
| Image processing | Pillow → pixelate → Pinata IPFS |
| Metadata | SEP-1 TOML hosted on IPFS |
| Explorer | [stellar.expert](https://stellar.expert) |

---

## 🏗️ Architecture

```
User pastes Tweet URL
        │
        ▼
┌───────────────────┐
│   Next.js Frontend │  ← Connect Freighter Wallet
└────────┬──────────┘
         │ POST /deploy
         ▼
┌───────────────────────────────────────┐
│           FastAPI Backend             │
│                                       │
│  ① Twitter API v2                     │
│     └─ Extract text, image, author   │
│                                       │
│  ② Metadata Builder                   │
│     └─ Name, Symbol, Supply (1B)     │
│     └─ Pixelate image → IPFS         │
│     └─ Generate SEP-1 TOML           │
│                                       │
│  ③ Stellar Asset Deployer             │
│     └─ Create Issuing Keypair        │
│     └─ Create Distribution Account  │
│     └─ Set Trustline                 │
│     └─ Issue Asset via Horizon       │
└────────┬──────────────────────────────┘
         │
         ▼
┌───────────────────┐
│  Stellar Network   │  ← Testnet / Mainnet
│  (Horizon API)     │
└────────┬──────────┘
         │
         ▼
┌───────────────────┐
│  Token Live ✅     │  ← Address + Explorer link returned
└───────────────────┘
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18+
- Python 3.11+
- Twitter Developer account (API v2 Bearer Token)
- Freighter wallet extension
- Pinata account (for IPFS)

### 1. Clone the repo

```bash
git clone https://github.com/yourusername/dexploy-stellar
cd dexploy-stellar
```

### 2. Backend setup

```bash
cd backend
pip install -r requirements.txt

cp .env.example .env
# Fill in: TWITTER_BEARER_TOKEN, PINATA_API_KEY, STELLAR_SECRET_KEY
```

```bash
uvicorn main:app --reload
```

### 3. Frontend setup

```bash
cd frontend
npm install

cp .env.local.example .env.local
# Fill in: NEXT_PUBLIC_BACKEND_URL, NEXT_PUBLIC_STELLAR_NETWORK
```

```bash
npm run dev
```

### 4. Open in browser

```
http://localhost:3000
```

Connect your Freighter wallet (set to **Testnet**), paste any tweet URL, and hit **Deploy Token**.

---

## 🔑 Environment Variables

### Backend (`.env`)

```env
TWITTER_BEARER_TOKEN=your_twitter_bearer_token
PINATA_API_KEY=your_pinata_api_key
PINATA_SECRET_KEY=your_pinata_secret_key
STELLAR_NETWORK=testnet          # or mainnet
STELLAR_HORIZON_URL=https://horizon-testnet.stellar.org
```

### Frontend (`.env.local`)

```env
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
NEXT_PUBLIC_STELLAR_NETWORK=testnet
```

---

## 📁 Project Structure

```
dexploy-stellar/
├── backend/
│   ├── main.py               # FastAPI app + routes
│   ├── scraper.py            # Twitter API v2 scraper
│   ├── token_builder.py      # Metadata generator
│   ├── image_processor.py    # Pixelate + IPFS upload
│   ├── stellar_deployer.py   # Horizon asset issuance
│   └── requirements.txt
│
├── frontend/
│   ├── app/
│   │   ├── page.tsx          # Main UI
│   │   └── result/page.tsx   # Token result page
│   ├── components/
│   │   ├── TweetInput.tsx
│   │   ├── TokenCard.tsx
│   │   └── WalletConnect.tsx
│   └── package.json
│
└── README.md
```

---

## 🧪 How Token Deployment Works (Stellar)

Dexploy uses Stellar's **native asset issuance** — no smart contract required for the base flow:

1. **Issuing Account** — a new keypair generated per token; holds the mint authority
2. **Distribution Account** — funded via the user's connected Freighter wallet
3. **Trustline** — the distribution account opts in to hold the new asset
4. **Payment** — the issuing account sends the full supply to the distribution account
5. **Lock** — the issuing account is optionally locked (auth flags cleared) to make supply fixed

All done via a single Stellar transaction (multi-op), confirmed in ~5 seconds.

> Optionally, a **Soroban SAC (Stellar Asset Contract)** can be deployed on top to enable bonding curves, fees, or DeFi integrations.

---

## 🗺️ Roadmap

- [x] Tweet scraping + metadata extraction
- [x] Pixel-art image transform
- [x] Stellar native asset issuance
- [x] IPFS metadata hosting (SEP-1 TOML)
- [ ] Soroban SAC wrapper for bonding curve
- [ ] Auto-listing on StellarX / SDEX
- [ ] Twitter bot integration (deploy from a reply)
- [ ] Multi-tweet monitoring (watch an account, deploy on every post)
- [ ] Token dashboard with holder stats

---

## 🤝 Contributing

PRs welcome. Open an issue first for major changes.

```bash
git checkout -b feature/your-feature
git commit -m "feat: your feature"
git push origin feature/your-feature
```

---

## ⚠️ Disclaimer

Tokens deployed via Dexploy are experimental assets on the Stellar network. This project is built for hackathon purposes. Do your own research before trading any token.

---

## 📄 License

MIT © 2025 — Built with ☀️ for the Stellar Hackathon
