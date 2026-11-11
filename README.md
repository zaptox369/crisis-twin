# Crisis Twin — Institutional Portfolio Stress Simulator & AI War Room

**Crisis Twin** is a full-stack quantitative flight simulator for equity portfolios. Instead of discovering portfolio vulnerabilities during a real market sell-off, Crisis Twin lets investors describe macroeconomic or geopolitical shocks in plain English, trace multi-hop contagion paths across sectors and holdings, and watch three adversarial AI advisors (**Bull**, **Bear**, and **Risk Auditor**) debate a mathematically grounded mitigation plan.

---

## Key Capabilities

### 1. Quantitative Factor Stress Engine (`lib/server/quant-engine.js`)
- Simulates multi-factor shocks across **Crude Oil (`oil_price`)**, **Monetary Policy Rates (`policy_rate` in bps)**, **FX (`usd_inr`)**, **Market Volatility (`volatility`)**, and **Geopolitical Risk**.
- Computes holding-level betas, horizon scaling ($\sqrt{T / 10}$), systemic stress correlation drag, **5th / 95th percentile bounds**, **CVaR-95**, and counterfactual post-mitigation drawdowns (`after_fix_pct`).
- Evaluates empirical historical volatility boundaries to assign transparent confidence ratings (`High`, `Medium`, `Low`).

### 2. Multi-Hop Contagion & Hidden Exposure Graph (`lib/server/propagation-engine.js`)
- Interactive SVG propagation topology (**Shock → Sector → Holding**) with BFS transmission and a **Sector Contagion Vulnerability Index (0–100)**.
- Surfaces cross-sector supply-chain and credit linkages that standard sector allocation views miss (e.g., banking credit tightening spilling over into auto financing, or aviation turbine fuel squeezing airline margins).

### 3. Adversarial Multi-Agent Debate Room (`lib/server/debate-orchestrator.js` & `lib/server/finbert.js`)
- Orchestrates a structured institutional debate grounded strictly on quantitative simulation outputs and **ProsusAI/FinBERT** financial sentiment analysis:
  - **Bull Advisor:** Identifies natural export/refining hedges (e.g., GRM expansion, USD revenue tailwinds) and domestic institutional liquidity floors.
  - **Bear Advisor:** Challenges concentration risk, current-account deficit pressure, and correlated cyclical drawdowns.
  - **Risk Auditor:** Audits cross-asset correlation spikes in tail events and synthesizes executable rebalancing actions with one-click paper-trade execution.

### 4. Voice War Room & Tool-Calling Strategist (`lib/server/groq-voice-war-room-chat.js`)
- Hands-free voice command interface with real-time audio visualization, natural-language scenario parsing (`lib/server/scenario-parser.js`), and deterministic server-side tool execution (`get_portfolio_summary`, `get_holding_details`, `get_sector_exposure`, `get_simulation_history`, `run_stress_test`).

### 5. Live Market Data, Paper Trading & Regret Replay
- **Holdings & Paper Trading (`/holdings`):** Import portfolios via CSV/JSON or add positions manually with Yahoo Finance symbol search (`/api/stocks/search`) and one-click live market quote refresh (`POST /api/portfolio/refresh`). Supports Market and Pending Limit orders with reserved cash/share accounting.
- **Regret Replay (`/replay`):** Re-runs historical crashes (**2008 GFC**, **2020 COVID Crash**, **2022 Rate Hike Cycle**, **Oil Spike**) against your actual portfolio weights to compare unprotected vs. mitigated drawdowns.
- **Crisis Drill (`/drill`), Report Card (`/report`) & Audit Trail (`/audit`):** Timed decision-making drills, printable executive report cards with survival scores, and full auditability of every simulation run.

---

## Tech Stack

- **Framework:** Next.js 16 (App Router, Turbopack, React 19)
- **Styling & Visualization:** Tailwind CSS v4 (Obsidian Flight Deck theme), custom zero-dependency SVG visualizations (`ShockGraph`, `LineChart`, `DivergingBar`, `RangeBar`, `Ring`)
- **AI & NLP:** Groq LLM cascade (`openai/gpt-oss-120b` / `qwen/qwen3.8-27b` / `openai/gpt-oss-20b`) with tool calling, Hugging Face `ProsusAI/finbert` sentiment scoring with deterministic financial lexicon fallback
- **Market Data:** Yahoo Finance symbol lookup & chart quote integration (`query1.finance.yahoo.com`)

---

## Getting Started Locally

### Prerequisites
- **Node.js** 20.9+ (Node.js 22 LTS recommended)
- **npm**

### 1. Install dependencies
```bash
npm install
```

### 2. Configure environment variables (optional for live AI / sentiment)
Create a `.env.local` file in the root directory:
```env
GROQ_API_KEY=your_groq_api_key
HF_TOKEN=your_huggingface_token
```
*(Note: Crisis Twin includes deterministic quantitative and rule-based fallbacks, so all core features run even without external API keys.)*

### 3. Start the development server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## API Overview

All API endpoints are served from Next.js Route Handlers under `/api`:

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/api/portfolio` | `GET` | Portfolio summary, P/L, weights, and reserved cash/shares |
| `/api/portfolio/import` | `POST` | Bulk import holdings and cash balance |
| `/api/portfolio/holdings` | `POST` / `PUT` / `DELETE` | Add, edit, or remove individual holdings |
| `/api/portfolio/refresh` | `POST` | Refresh latest market prices & daily % changes from Yahoo Finance |
| `/api/stocks/search` | `GET` | Live equity & ETF ticker lookup across NSE, BSE, NYSE, and NASDAQ |
| `/api/scenario/parse` | `POST` | Parse plain-English crisis scenarios into structured factor shocks |
| `/api/simulate` | `POST` | Run quantitative factor stress simulation with mitigation plan |
| `/api/propagate` | `GET` / `POST` | Compute multi-hop sector & holding contagion graph |
| `/api/debate` | `POST` | Generate Bull / Bear / Risk Auditor adversarial debate & rebuttals |
| `/api/intelligence/chat` | `POST` | Voice War Room tool-calling strategist |
| `/api/paper/trade` | `POST` | Execute market or queued limit paper trades |
| `/api/replay/:crisisId` | `GET` | Portfolio-calibrated historical crisis replay |
| `/api/report/:runId` | `GET` | Executive crisis report card & counterfactual survival score |
| `/api/audit/runs` | `GET` / `DELETE` | Full audit trail of saved simulations and advisor logs |

---

## Disclaimer

Crisis Twin is an educational quantitative simulation and decision-support system. It does not provide financial advice and never executes real brokerage trades.
