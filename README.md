╔══════════════════════════════════════════════════════════════════╗
║              🛡️  SHIELD SCAN — PROJECT OVERVIEW                 ║
║                    [ quantum.html / Quantum Arena ]              ║
╚══════════════════════════════════════════════════════════════════╝

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  WHAT IS IT?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  SHIELD SCAN is an AI-powered phishing URL detection tool built
  as a single self-contained HTML file (quantum.html). It lets
  users paste any URL and instantly receive a threat verdict —
  SAFE, SUSPICIOUS, or DANGEROUS — with a full breakdown of why.

  No server. No installation. No dependencies.
  Just open the file in a browser and it works.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  CORE IDEA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Phishing links are one of the most common attack vectors in
  cybersecurity. Most people can't tell a malicious URL from a
  legitimate one just by looking at it. SHIELD SCAN solves this
  by running a multi-factor analysis on the URL structure itself
  and returning an instant, explainable risk score.

  The prototype proves that a meaningful threat detector can run
  100% client-side — no backend, no API key, no cost.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  HOW IT WORKS — 4-STEP SCAN PIPELINE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  STEP 1 │ VALIDATE
         │ Checks that the input is a properly formatted URL
         │ (must include http:// or https://)

  STEP 2 │ EXIST CHECK
         │ Sends a real network request to verify the URL is
         │ reachable. If the domain doesn't exist (DNS failure
         │ or timeout), the scan stops and reports "NOT FOUND"

  STEP 3 │ ANALYZE
         │ Runs 7 independent threat checks on the URL string.
         │ Each check adds points to a risk score (0–100).

  STEP 4 │ RESULT
         │ Displays the verdict, animated risk bar, and a
         │ full breakdown of every risk factor detected.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  THREAT SCORING ENGINE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Risk Factor                    │ Score Added
  ───────────────────────────────┼─────────────
  No HTTPS (HTTP only)           │ +15
  Suspicious TLD (.tk .ml .xyz)  │ +25
  IP address used as host        │ +30
  Phishing keywords in URL       │ +8 per word
  Urgency/scare tactic words     │ +6 per word
  @ symbol in URL                │ +30
  3+ hyphens in domain           │ +15

  VERDICT THRESHOLDS:
  ┌──────────────┬───────────────────────────────────────┐
  │  Score 0–34  │  ✅  SAFE       — Appears legitimate  │
  │  Score 35–59 │  ⚠️  SUSPICIOUS — Exercise caution    │
  │  Score 60+   │  🚨  DANGEROUS  — High phishing risk  │
  │  Unreachable │  💀  NOT FOUND  — URL does not exist  │
  └──────────────┴───────────────────────────────────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  KEY FEATURES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ◈  Real-time URL analysis — results in under 3 seconds
  ◈  Live terminal log — shows every scan step as it happens
  ◈  Animated risk bar — visually communicates threat level
  ◈  Toast notifications — instant color-coded alerts
  ◈  Session statistics — tracks scans, threats, safe URLs
  ◈  Cyberpunk UI — animated grid, floating particles, glow FX
  ◈  Fully responsive — works on desktop and mobile
  ◈  Zero dependencies — no npm, no framework, no build step

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  TECH STACK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Language    │  Vanilla HTML / CSS / JavaScript
  Fonts       │  Orbitron (headings) + Share Tech Mono (body)
  Network     │  Browser Fetch API (no-cors mode)
  Animations  │  Pure CSS keyframes + JS DOM manipulation
  Storage     │  In-memory only (no localStorage / database)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  PROJECT FILES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  quantum.html    →  Full working prototype (single file, ~1559 lines)
  README.md       →  Project overview & quick start guide
  architecture.md →  System design, data flow & component breakdown
  prototype.md    →  Implementation details & code walkthrough

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  QUICK START
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  1. Clone or download the repository
  2. Open quantum.html in any modern browser
  3. Enter a URL in the input field
  4. Click "Initialize Scan" or press Enter
  5. Read the verdict and analysis breakdown

  No setup. No server. No configuration needed.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  DEMO TEST CASES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  URL                                      │ Expected Result
  ─────────────────────────────────────────┼─────────────────
  https://google.com                       │ ✅ SAFE
  http://google.com                        │ ⚠️  Low risk
  http://secure-login.tk                   │ 🚨 DANGEROUS
  https://192.168.1.1/login                │ 🚨 DANGEROUS
  https://paypal-verify-urgent.xyz         │ 🚨 DANGEROUS
  https://thisdoesnotexist99999.com        │ 💀 NOT FOUND

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  CURRENT LIMITATIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  • Heuristic rules only — no trained ML model
  • Cannot inspect page content, only the URL string
  • Existence check is DNS-level (can't detect HTTP 404/500)
  • Session stats reset on page reload (no persistence)
  • Small keyword list — easily extended but limited by default

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  FUTURE ROADMAP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  [ ] Integrate VirusTotal / Google Safe Browsing API
  [ ] Domain WHOIS age check (new domains = higher risk)
  [ ] Browser extension version
  [ ] Persistent scan history via localStorage
  [ ] Export scan report as PDF
  [ ] ML-based scoring model to replace rule engine

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Built by AI Alchemist  |  Powered by SHIELD SCAN Protocol

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
