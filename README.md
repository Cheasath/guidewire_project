# 🛡️ GigShield AI
### Parametric Income Protection for India's Gig Delivery Workers
**Guidewire DEVTrails 2026 · University Hackathon · Phase 1 Submission**

---

| 1.2 Cr Gig Workers Targeted | $29.3B Parametric Market by 2031 | ₹65/week Avg Premium | <2 hrs Automated Claim Payout |
|:---:|:---:|:---:|:---:|

---

## 1. Executive Summary

**GigShield AI** is a parametric income protection platform built for food delivery partners (Swiggy/Zomato) in Tier-1 Indian cities. When an external disruption — extreme rain, severe air pollution, platform outage, or curfew — prevents delivery workers from earning, GigShield automatically detects the event via government APIs, and credits lost wages to the worker's UPI account within 2 hours, with **zero manual claim filing required**.

Built around three core principles:
- **Parametric-first:** External data triggers claims — no worker action needed. Zero moral hazard.
- **Insurer profitability focus:** Loss ratio optimizer dashboard with ML-driven reserve alerts.
- **Explainable AI:** Every claim decision — approved or flagged — comes with plain-language reasoning visible to the worker.

> India has 1.2 crore gig delivery workers, 80% with zero income protection. The global parametric insurance market is projected to reach $29.3 billion by 2031 (EY Global Insurance Outlook 2025). GigShield targets the most urgent, underserved slice of that market.

---

## 2. Problem Statement

India's platform-based delivery partners (Zomato, Swiggy, Zepto, Amazon, Dunzo) lose **20–30% of monthly income** due to external disruptions — extreme weather, pollution, curfews — with no safety net.

### Demo Persona

| Attribute | Detail |
|---|---|
| Name | Rajan Kumar |
| Age | 27 |
| Platform | Swiggy Food Delivery |
| Zone | Velachery, Chennai (coastal, flood-prone) |
| Avg Daily Income | ₹800 (10–12 working hours) |
| Peak Risk Season | Northeast Monsoon (Oct–Dec) |
| Days Lost/Month (monsoon) | 3–4 days |
| Monthly Income at Risk | ₹2,400–₹3,200 |
| GigShield Weekly Premium | ₹65 |
| Coverage if Triggered | ₹1,600 |
| Net Gain in Disruption Week | ₹1,535 |

### Why Food Delivery Partners
- Largest gig sub-segment: 15+ lakh active monthly partners across Tier-1 cities
- Most directly impacted by weather: rain events instantly kill order volumes
- Well-documented income data from Zomato/Swiggy public disclosures
- Weekly earnings cycle aligns naturally with a weekly premium model
- Most active policy discourse (Social Security Code 2020, enacted Nov 2025)

---

## 3. How It Works — End-to-End Flow

| Step | Actor | Action | Technology |
|---|---|---|---|
| 1 | Worker | Registers on GigShield via Swiggy onboarding (2 min) | React PWA + Node.js API |
| 2 | AI System | Calculates weekly premium using XGBoost (every Monday 6 AM) | Python FastAPI + XGBoost |
| 3 | System | Auto-deducts premium from UPI mandate every Sunday | Razorpay AutoPay API |
| 4 | Trigger Engine | Polls IMD/CPCB/OWM APIs every 15 minutes for disruptions | Node.js cron + Redis |
| 5 | Claim Engine | Threshold crossed → batch-initiate claims for all zone workers | PostgreSQL + Redis queue |
| 6 | Fraud AI | Runs 7-signal fraud detection on each claim in parallel (1.8s) | Isolation Forest + composite model |
| 7 | AutoPay | Score ≥70 → auto-approve → UPI payout within 2 hours | Razorpay Payout API |
| 8 | Worker | Receives payment notification with plain-language explanation | SMS + Push notification |

---

## 4. Weekly Premium Model

### Formula

```
Premium = BaseRate(₹30) × ZoneRisk(1.0–2.5) × WeatherScore(1.0–1.8) × ActivityScore(0.8–1.0) × WIISDiscount(0.9–1.0)
```

### Factor Details

| Factor | Range | Data Source | Purpose |
|---|---|---|---|
| Base Rate | ₹30 fixed | Actuarial calculation | Floor premium covering basic insurer cost |
| Zone Risk Multiplier | 1.0–2.5× | Historical disruption days/year per PIN | Flood-prone zones cost more; low-risk zones stay affordable |
| Weather Forecast Score | 1.0–1.8× | IMD / OpenWeatherMap 7-day forecast | Next week looks stormy? Premium rises to match risk |
| Activity Score | 0.8–1.0× | Worker's active days (last 4 weeks) | Regular workers get a loyalty discount |
| WIIS Discount | 0.9–1.0× | Worker Income Intelligence Score | High-trust workers (WIIS >80) pay less |
| Output Range | ₹20–₹150/week | XGBoost model prediction | Affordable for all income levels |

### Rajan's Transparent Premium Breakdown

| Component | Amount | Explanation |
|---|---|---|
| Base Rate | ₹30 | Standard weekly floor |
| Zone Risk (Velachery, 2.1×) | +₹20 | High flood-risk zone, 72 disruption days/year historically |
| Weather Forecast (1.35×) | +₹15 | IMD predicts moderate monsoon risk next week |
| WIIS Discount (score: 74) | -₹0 | Silver tier — Gold (≥80) gets ₹15 off |
| **Weekly Total** | **₹65** | Auto-deducted Sunday from UPI mandate |
| Coverage if Triggered | ₹1,600 | (₹800/day ÷ 10hr) × 4hr disruption × 2 covered days |

---

## 5. Parametric Triggers

GigShield monitors **5 objective, third-party verified triggers** every 15 minutes. Workers never file a claim — the system detects the disruption and initiates payouts automatically.

| # | Trigger | Source | Threshold | Impact Covered |
|---|---|---|---|---|
| T1 | Extreme Rainfall | IMD / OpenWeatherMap API | ≥64mm/3hr (Red Alert) | Orders halted — delivery impossible |
| T2 | Severe Air Quality | CPCB Government AQI API | AQI ≥300 (Severe), sustained 2+ hours | Outdoor work dangerous to health |
| T3 | Platform Outage | Swiggy/Zomato mock API webhook | Downtime >30 minutes | No orders can be placed or accepted |
| T4 | Curfew / Bandh | NLP classifier on news RSS feed | Section 144 / bandh detected in zone | Zone access blocked — no deliveries possible |
| T5 | Extreme Temperature | IMD temperature advisory | >44°C heat or <5°C cold wave | Work conditions unsafe or legally restricted |

### Payout Calculation
```
Payout = (avg_daily_income ÷ 10 working hours) × disruption_hours × coverage_multiplier
```
- Maximum payout: **₹500 per day** — capped to prevent over-insurance
- Maximum covered days: **3 days per week**
- Payout channel: **Razorpay UPI** — direct to worker's registered UPI handle within 2 hours

---

## 6. AI / ML Integration

| Model | Algorithm | Purpose | Training Data |
|---|---|---|---|
| Premium Engine | XGBoost Regressor | Calculates weekly premium for each worker every Monday | 5,000 synthetic samples (zone × weather × worker activity × income) |
| Fraud Detection | 7-Signal Composite + Isolation Forest | Scores each claim 0–100; detects coordinated ring attacks | 3,000 labelled fraud/legitimate samples (30% contamination) |
| WIIS Score | Weighted Regression (5 factors) | Calculates portable income trust score for each worker | Rule-based with calibrated weights |
| Claim Forecasting | Seasonal Baseline + Weather Signal | Predicts next-week claim volume and loss ratio by zone | Zone historical disruption data + IMD seasonal patterns |
| Curfew Detection | spaCy NLP Keyword Classifier | Detects curfew/bandh events from news RSS feeds | Labelled keyword set (section 144, bandh, hartal, etc.) |

### XGBoost Premium Model
- **Input features (6):** `zone_risk_score`, `weather_forecast_score`, `activity_score`, `wiis_score`, `avg_daily_income`, `wiis_discount`
- **Hyperparameters:** 200 estimators, max_depth=5, learning_rate=0.05, subsample=0.8, colsample_bytree=0.8
- **Output:** `weekly_premium` (₹20–150) + `coverage_amount` + explainable breakdown per factor
- **Why XGBoost:** Captures non-linear interactions between zone risk and weather better than linear models; fast enough for batch weekly recalculation across 500+ workers

---

## 7. Adversarial Defense & Anti-Spoofing Strategy

> **🚨 Market Crash Scenario:** A sophisticated syndicate of 500 delivery workers in a Tier-1 city, organizing via Telegram, used GPS-spoofing apps to fake their locations in a Red Alert weather zone while resting safely at home — triggering mass false payouts and draining a competitor platform's liquidity pool.

### 7.1 The Differentiation — Architectural Spoofing Resistance

GigShield's most important fraud-prevention feature is **architectural, not algorithmic**. The parametric TRIGGER is sourced from objective third-party government APIs — IMD rainfall data, CPCB AQI readings. These cannot be manipulated by workers.

**Traditional insurance (spoofable):**
```
Worker reports location → GPS verified → Payout
```

**GigShield (spoofing-resistant):**
```
IMD/CPCB data crosses threshold → Claims initiated → 7-signal fraud score → GPS is only 15% of score
```

A Telegram fraud ring cannot change what IMD records on their government servers. GPS is used only as Signal #1 of 7 in post-trigger fraud scoring, weighted at just **15 of 100 points**.

### 7.2 The Data — 7 Signals Beyond GPS

| Signal | Weight | What It Detects |
|---|---|---|
| GPS zone match | 15 pts | Worker's GPS in the claimed disruption zone (necessary but alone insufficient) |
| Cell tower pattern | 20 pts | Home Wi-Fi base station ID vs. road tower trajectory — stationary home pattern = flag |
| Accelerometer variance | 20 pts | <0.3 m/s² over 4+ hours during outdoor disruption = stationary at home |
| Platform order log | 15 pts | Genuine disruption: platform shows no orders in zone. Fraud: worker rejecting available orders |
| Cluster anomaly | 15 pts | ≥20 simultaneous claims from same 6-digit PIN in 30 min → Isolation Forest ring detection |
| Historical claim frequency | 10 pts | Claiming >60% of weeks → Poisson distribution anomaly → elevated scrutiny |
| IP geolocation delta | 5 pts | Home broadband IP subnet differs from mobile data IP in claimed outdoor zone |
| WIIS trust bonus | +5 pts | High-trust workers (WIIS >80) get benefit of the doubt across all signals |

The compound-signal approach makes fraud **exponentially harder**. A sophisticated attacker can spoof GPS. They cannot simultaneously spoof GPS + disable accelerometer movement + fake cell tower trajectory + manipulate platform order logs + avoid statistical clustering + maintain low claim frequency + match IP subnet to outdoor location. All 7 must align simultaneously.

### 7.3 The UX Balance — Protecting Honest Workers

| Fraud Score | Action | Worker Experience |
|---|---|---|
| ≥70 (auto-approve) | Instant claim approval + payout | No friction — money credited within 2 hours with explanation |
| 40–69 (soft-flag) | Quick review — 4-hour SLA | "Your claim is under quick review. Your payment is NOT blocked." Payment proceeds if review clears. |
| <40 (hard-flag) | Hold + appeal flow | "Your claim is on hold. Tap to appeal with evidence." One-tap photo/video appeal submission. |
| First offence | Benefit of doubt + warning | Educational notification explaining flag; no penalty on first incident |
| Network drop exception | Auto-cleared | GPS loss in bad weather + cell towers confirm zone + accelerometer shows movement = auto-clear, no penalty |

---

## 8. Worker Income Intelligence Score (WIIS)

WIIS is GigShield's **portable income reputation score** (0–100) — like a credit score but for gig work. It follows the worker across platforms (Swiggy, Zomato, Zepto, Blinkit) and determines premium discounts, coverage tier, and claim processing speed.

| Factor | Weight | Detail |
|---|---|---|
| Platform tenure | 25 pts | 24 months = full score. Rewards long-term committed workers. |
| Activity consistency | 30 pts | 22+ active days/month = full score. Regular work = reliable income = lower risk. |
| Income stability | 20 pts | ₹1,200+/day = full score. Higher earners are statistically lower fraud risk. |
| Claim legitimacy | 20 pts | No fraudulent claims = full score. Drops after confirmed false claims. |
| Zone risk adaptation | 5 pts | Working in high-risk zones shows dedication; rewarded with bonus points. |

### WIIS Tiers & Benefits

| Tier | Score Range | Premium Benefit | Coverage Benefit | Claim Processing |
|---|---|---|---|---|
| 🥇 GigShield Gold | 80–100 | ₹15/week discount | 3× income coverage | Instant auto-approval |
| 🥈 GigShield Silver | 60–79 | ₹8/week discount | 2× income coverage | Fast-track review |
| 🥉 GigShield Bronze | 40–59 | Standard pricing | 1.5× income coverage | Standard processing |
| Building Trust | <40 | Standard pricing | 1× income coverage | Standard processing |

**Why WIIS is the Unicorn Differentiator:**
- **Platform portability:** Score travels with the worker — Zomato WIIS transfers to Swiggy. First portable gig income identity in India.
- **Fraud deterrence:** Workers who build a high WIIS risk losing premium discounts by filing false claims — a strong economic disincentive.
- **Financial inclusion potential:** WIIS could be used by partner banks for micro-loan eligibility — a future revenue stream beyond insurance.

---

## 9. Tech Stack & Architecture

| Layer | Technology | Justification |
|---|---|---|
| Frontend | React.js PWA + Tailwind CSS + Chart.js | PWA = no app-store friction for gig workers. Works offline. |
| Backend API | Node.js + Express | Fast REST API, excellent ecosystem, cron job support for trigger polling |
| ML Microservice | Python FastAPI | Native ML libraries (XGBoost, scikit-learn, spaCy). Isolated, independently scalable. |
| Database | PostgreSQL + Redis | Relational integrity for policies/claims; JSONB for flexible payloads; Redis for trigger queuing |
| ML Libraries | XGBoost, scikit-learn, Isolation Forest, spaCy | Real ML models — no mock predictions |
| Weather API | OpenWeatherMap (free tier) | Real rain and temperature data; generous free tier for demos |
| AQI API | CPCB Government API | Official India government AQI data — authoritative, free, verifiable |
| Payments | Razorpay (sandbox) | UPI AutoPay mandate + instant payout simulation |
| Deployment | Docker Compose | Single command starts all 4 services; deployable to any cloud in minutes |

### Data Models

| Entity | Key Fields |
|---|---|
| Worker | `worker_id`, `name`, `phone`, `zone_id`, `platform`, `avg_daily_income`, `wiis_score`, `bank_upi` |
| Policy | `policy_id`, `worker_id`, `week_start`, `week_end`, `weekly_premium`, `coverage_amount`, `premium_breakdown` (JSONB) |
| DisruptionEvent | `event_id`, `zone_id`, `trigger_type`, `severity`, `trigger_value`, `threshold_value`, `api_source`, `raw_payload` |
| Claim | `claim_id`, `policy_id`, `event_id`, `worker_id`, `claim_amount`, `fraud_score`, `status`, `payout_time`, `explainability_text` |
| FraudSignal | `signal_id`, `claim_id`, `gps_score`, `cell_tower_score`, `accelerometer_score`, `order_log_score`, `cluster_score`, `hist_score`, `ip_score`, `composite_score` |
| Zone | `zone_id`, `city`, `state`, `pin_codes[]`, `lat`, `lng`, `historical_disruption_days`, `base_risk_score` |

---

## 10. Quick Start

### One-Command Startup (Docker)

```bash
# Clone the repository
git clone https://github.com/[your-team]/gigshield-ai
cd gigshield-ai

# Add your free API key (openweathermap.org)
cp .env.example .env
# Edit .env: OPENWEATHER_API_KEY=your_key_here

# Start all services
docker-compose up --build
```

### Access Points

| Service | URL | Notes |
|---|---|---|
| Frontend (full demo) | http://localhost:3000 | Open `index.html` directly — works without backend |
| Backend API | http://localhost:4000 | REST API + cron trigger service |
| ML Microservice | http://localhost:8000 | FastAPI + Swagger UI at `/docs` |
| API Health | http://localhost:4000/health | System status check |
| ML Health | http://localhost:8000/health | Model status and training confirmation |

### Demo Credentials

| Role | Credential | Value |
|---|---|---|
| Delivery Partner | Mobile Number | 9876543210 (any 10-digit works) |
| Delivery Partner | OTP | 1234 |
| Insurer Admin | Email | admin@gigshield.in |
| Insurer Admin | Password | admin123 |

### Repository Structure

```
gigshield-ai/
├── frontend/
│   └── public/index.html       # Standalone demo — works without backend
├── backend/
│   ├── server.js               # Express API + cron jobs
│   ├── schema.sql              # PostgreSQL schema + 10 zone seed data
│   ├── routes/                 # workers, policies, claims, demo, dashboard
│   └── services/               # triggerService, premiumService
├── ml-service/
│   ├── main.py                 # FastAPI endpoints
│   └── models/                 # premium, fraud, wiis, forecast models
├── docker-compose.yml          # One-command startup
├── .env.example                # API key template
└── README.md
```

---

## 11. Phase 1 Deliverables

| Deliverable | Status | Notes |
|---|---|---|
| README document (this file) | ✅ Complete | All 8 required sections + Market Crash response |
| GitHub repository | ✅ Live | Same repo used for all subsequent phases |
| 2-minute demo video | ✅ Uploaded | Strategy, prototype walkthrough, anti-spoofing explanation |
| Adversarial Defense & Anti-Spoofing Strategy | ✅ Included | Section 7 — all 3 required subsections addressed |
| Weekly premium model explanation | ✅ Included | Section 4 — formula, factors, transparent breakdown |
| Parametric trigger definitions | ✅ Included | Section 5 — all 5 triggers with source, threshold, and payout logic |
| AI/ML integration plan | ✅ Included | Section 6 — 5 models with algorithm, purpose, and training data |
| Tech stack and development plan | ✅ Included | Section 9 — full stack + 3-phase timeline |

### Prototype Scope

**Working in this submission (Phase 1):**
Complete React PWA frontend with all 8 modules; Worker login with phone OTP; Admin login with email/password; Live OpenWeatherMap API integration; 7-signal fraud detection running in browser; XGBoost premium formula; WIIS scoring; Razorpay payment modal; AI Assistant with Claude API; Full demo pipeline animation.

**Phase 2 additions (Mar 21 – Apr 4):**
XGBoost model trained on PostgreSQL-stored synthetic data; Live CPCB AQI API integration; Razorpay sandbox UPI payout calls; Real-time trigger engine with Redis; Isolation Forest trained model; 2-minute Phase 2 demo video.

**Phase 3 additions (Apr 5–17):**
Full fraud ring detection; Prophet claim volume forecasting; WIIS history tracking; Final pitch deck; 5-minute walkthrough video with live disruption simulation.

---

## 12. Business Model

### Revenue Streams

| Revenue Stream | Model | Estimate |
|---|---|---|
| Premium spread (primary) | 30–35% margin on weekly premium | ₹1,409/worker/year at 3-star performance |
| SaaS platform fee | ₹2/policy/week from insurance partners | ₹4.8Cr/year at 500K workers |
| B2B integration fee | ₹5L/year per platform partner | ₹25L/year for 5 platforms |
| Data intelligence (future) | Anonymized zone risk data to reinsurers | ₹50–200/zone/month |
| WIIS micro-loan eligibility (future) | Partner bank licensing fee | TBD — pending RBI fintech sandbox approval |

### Unit Economics
- **Annual premium per worker:** ₹65 × 52 weeks = ₹3,380/year
- **Expected claims cost:** ₹3,380 × 58.3% loss ratio = ₹1,971/year
- **GigShield margin per worker:** ~₹1,409/year (after claims, before platform/fraud costs)
- **Break-even:** ~8,500 active policies (achievable in 6 months in one Tier-1 city)
- **At 1% penetration (120,000 workers): ₹16.9 Cr annual gross margin**

The Social Security Code 2020 (enacted November 2025) mandates welfare funds for gig workers but does not cover income loss from weather or disruptions — **that gap is GigShield's entire TAM**.

---

## 13. Why GigShield AI Wins

| What Winners Do | How GigShield Delivers It |
|---|---|
| Solve a REAL market problem with quantified TAM | 1.2 Cr gig workers, 80% unprotected, $29.3B parametric market by 2031 (EY 2025) |
| Have a WORKING demo, not just slides | Full React PWA with real OpenWeatherMap API, live 10-step AutoPay pipeline, Razorpay payment modal |
| Show AI doing something non-trivial and explainable | XGBoost premium engine + 7-signal fraud composite + Isolation Forest ring detection + WIIS scoring, all with plain-language explainability |
| Understand the INSURER's P&L, not just end user | Loss ratio optimizer dashboard, reserve alerts, zone-wise breakdown, premium adjustment recommendations — exactly what Guidewire PricingCenter targets |
| Have a business model that makes money | Premium spread + SaaS fee + B2B integration fee. Break-even at 8,500 workers. ₹16.9Cr margin at 1% market penetration |
| Match Guidewire's 2025 product vision | Dynamic AI pricing (PricingCenter), agentic claims automation (ClaimCenter), parametric triggers, explainable AI — direct alignment with Guidewire Olos release |
| Go beyond the brief with innovation | WIIS Score: India's first portable gig income identity. Turns insurance into a financial reputation layer across all gig platforms |

---

> *"We're not just building a hackathon project. We're building the safety net India's 1.2 crore gig workers never had."*

**GigShield AI · Guidewire DEVTrails 2026 · University Hackathon · Phase 1 Submission**
