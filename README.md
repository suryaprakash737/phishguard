
# PhishGuard 🛡️

**AI-Powered Phishing Email Analyzer for Security Teams and End Users**

Live demo: [(https://phishguard-s3dt.onrender.com/)](https://phishguard-s3dt.onrender.com/)]

---

## What it does

Phishing is the #1 attack vector in cybersecurity. 97% of users cannot reliably identify sophisticated phishing emails. PhishGuard Email Coach lets anyone paste a suspicious email and get an instant, AI-powered threat assessment in seconds.

**Without PhishGuard Email Coach:** A SOC analyst or end user receives a suspicious email, manually cross-references sender domains, inspects URLs, checks headers, and writes an incident report — taking 15–20 minutes per email.

**With PhishGuard Email Coach:** Paste the email → get risk level, indicator breakdown, action guide, and a copy-paste IT report in under 10 seconds.

---

## Features

### For end users
- **Risk badge** — HIGH / MEDIUM / LOW with confidence score
- **Plain-English explanations** — no technical jargon
- **Action guide** — exactly what to do next
- **Highlighted email** — suspicious phrases marked directly in the email body
- **One-click IT report** — pre-filled report ready to forward to your security team

### For security analysts
- **Analyst Mode** — toggle for technical output including IOCs, MITRE ATT&CK techniques, and domain forensics
- **Header parsing** — automated extraction of From, Reply-To, domain mismatch detection
- **URL extraction** — all links pulled and analyzed
- **MITRE ATT&CK mapping** — techniques like T1566 (Spear Phishing) identified automatically

### Privacy by design
- Emails are **never stored** on our servers
- Only an anonymous 8-character hash is logged for analytics
- Users warned not to paste sensitive credentials

---

## Architecture

```
User (Browser)
      ↓
React-style Frontend (HTML/CSS/JS)
      ↓
FastAPI Backend (Python)
  ├── extract_metadata()    ← Python email library + regex
  │     extracts: From, Reply-To, domain mismatch, URLs
  ├── call_codex()          ← OpenAI gpt-4o-mini API
  │     prompt includes: metadata + full email + mode
  └── build_report()        ← Template formatter
      ↓
OpenAI API (gpt-4o-mini)
      ↓
JSON Response:
  risk_level, confidence, verdict,
  highlighted_phrases, indicators,
  recommended_actions, analyst_notes
      ↓
Frontend renders:
  Risk badge → Indicator cards → Action steps
  → Highlighted email → Copy IT Report button
```

---

## Tech stack

| Layer | Technology |
|---|---|
| Backend | Python 3.13, FastAPI, Uvicorn |
| AI | OpenAI gpt-4o-mini via Responses API |
| Email parsing | Python `email` stdlib + `re` |
| Frontend | HTML5, CSS3, Vanilla JS |
| Deployment | Railway |

---

## Running locally

**Prerequisites:** Python 3.10+, OpenAI API key

```bash
git clone https://github.com/suryaprakash737/PhishGuard Email Coach
cd PhishGuard Email Coach/backend
python -m venv venv
venv\Scripts\activate        # Windows
pip install -r requirements.txt
```

Create `.env`:
```
OPENAI_API_KEY=sk-proj-your-key-here
```

Start the server:
```bash
uvicorn main:app --reload
```

Open http://127.0.0.1:8000

---

## API endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/analyze` | Analyze email — returns full JSON threat assessment |
| POST | `/report` | Generate formatted IT incident report |
| GET | `/examples` | Load built-in sample phishing emails |
| GET | `/health` | Health check |

### Example request
```bash
curl -X POST https://web-production-8fc7d.up.railway.app/analyze \
  -H "Content-Type: application/json" \
  -d '{"email_text": "From: security@paypa1-support.ru\nYour account will be suspended...", "mode": "analyst"}'
```

### Example response
```json
{
  "risk_level": "HIGH",
  "confidence": 95,
  "verdict": "Phishing attempt impersonating PayPal using domain spoofing.",
  "highlighted_phrases": [
    {"text": "Your account will be suspended", "reason": "Urgency manipulation"},
    {"text": "Click here immediately", "reason": "Pressure tactic"}
  ],
  "indicators": [
    {"text": "paypa1-support.ru", "reason": "Homoglyph domain spoof", "severity": "HIGH"},
    {"text": "Reply-To: harvester@evil.com", "reason": "Credential harvesting address", "severity": "HIGH"}
  ],
  "recommended_actions": ["Do not click links", "Report to IT security"],
  "analyst_notes": "T1566.001 Spear Phishing Link. Domain mismatch confirmed. IOCs: paypa1-support.ru, paypa1-verify.ru"
}
```

---

## Project structure

```
PhishGuard Email Coach/
├── backend/
│   ├── main.py              # FastAPI app — all endpoints
│   ├── requirements.txt     # Python dependencies
│   ├── Procfile             # Railway deployment config
│   ├── .env                 # API key (not committed)
│   └── static/
│       └── index.html       # Full frontend
└── README.md
```

---

## Built by

Suryaprakash Uppalapati  
MS Computer Science, George Mason University (May 2026)  
Concentration: Machine Learning  
GitHub: https://github.com/suryaprakash737  

---

## Why this matters

Phishing accounts for over 80% of reported security incidents. Existing tools are either too technical for end users or too slow for high-volume SOC environments. PhishGuard Email Coach bridges that gap — giving non-technical users plain-English guidance while giving analysts the technical depth they need, all from the same interface in under 10 seconds.


