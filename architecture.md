╔══════════════════════════════════════════════════════════════════════════╗
║         🛡️  SHIELD SCAN — SYSTEM DESIGN & WORKFLOW                      ║
║                      [ quantum.html / Quantum Arena ]                    ║
╚══════════════════════════════════════════════════════════════════════════╝


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  1. SYSTEM ARCHITECTURE — BIG PICTURE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  SHIELD SCAN is a fully client-side, single-file web application.
  There is no backend server, no database, and no external API calls.
  Everything — UI, logic, and analysis — runs inside the user's browser.

  ┌─────────────────────────────────────────────────────────────────────┐
  │                        USER'S BROWSER                               │
  │                                                                     │
  │   ┌─────────────┐    ┌──────────────┐    ┌──────────────────────┐  │
  │   │             │    │              │    │                      │  │
  │   │   UI LAYER  │───▶│  CONTROLLER  │───▶│   ANALYSIS ENGINE    │  │
  │   │  (HTML/CSS) │    │  startScan() │    │ EnhancedURLAnalyzer  │  │
  │   │             │◀───│              │◀───│                      │  │
  │   └─────────────┘    └──────┬───────┘    └──────────────────────┘  │
  │                             │                                       │
  │                      ┌──────▼───────┐                              │
  │                      │  FETCH API   │                              │
  │                      │ (no-cors)    │──────────────────────────▶   │
  │                      │ Exist Check  │        INTERNET              │
  │                      └─────────────┘  (DNS / Target Server)        │
  └─────────────────────────────────────────────────────────────────────┘

  KEY DESIGN PRINCIPLE:
  The only outbound network call is the URL existence check (Step 2).
  All threat scoring is done locally — no data leaves the browser
  for analysis purposes.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  2. SYSTEM LAYERS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ┌──────────────────────────────────────────────────────────────────────┐
  │  LAYER 1 — PRESENTATION LAYER (HTML + CSS)                          │
  │                                                                      │
  │  • Header (title, tagline, AI badge)                                 │
  │  • Scanner Card (URL input + scan button)                            │
  │  • Terminal Output (live scan log)                                   │
  │  • Results Section (verdict card + risk bar + breakdown)             │
  │  • Scanning Overlay (full-screen animation + progress steps)         │
  │  • Alert System (toast notifications)                                │
  │  • Stats Row (session counters)                                      │
  │  • Background FX (animated grid + floating particles)                │
  ├──────────────────────────────────────────────────────────────────────┤
  │  LAYER 2 — CONTROL LAYER (JavaScript — Orchestration)               │
  │                                                                      │
  │  • startScan()         — main async scan orchestrator               │
  │  • isValidUrl()        — URL format validation                       │
  │  • checkUrlExists()    — network reachability check                  │
  │  • updateStep()        — progress indicator manager                  │
  │  • updateScanningStatus() — overlay status text updater             │
  │  • addTerminalLine()   — terminal log writer                         │
  │  • showAlert()         — toast notification trigger                  │
  ├──────────────────────────────────────────────────────────────────────┤
  │  LAYER 3 — ANALYSIS LAYER (JavaScript — Threat Engine)              │
  │                                                                      │
  │  • EnhancedURLAnalyzer.analyzeUrl()  — core scoring engine          │
  │  • 7 independent rule checks                                         │
  │  • Risk score accumulation (0–100)                                   │
  │  • Verdict classification (SAFE / SUSPICIOUS / PHISHING)            │
  ├──────────────────────────────────────────────────────────────────────┤
  │  LAYER 4 — RENDER LAYER (JavaScript — Output)                       │
  │                                                                      │
  │  • displayResults()      — renders full analysis card               │
  │  • displayUrlNotExist()  — renders "not found" card                 │
  │  • animateValue()        — animated number counter                  │
  │  • updateStats()         — session statistics updater               │
  └──────────────────────────────────────────────────────────────────────┘


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  3. COMPLETE WORKFLOW — STEP BY STEP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ┌─────────────────────────────────────────────────────────────────────┐
  │  USER ENTERS URL  →  Clicks "Initialize Scan" or presses Enter      │
  └──────────────────────────────┬──────────────────────────────────────┘
                                 │
                                 ▼
  ╔══════════════════════════════════════════════════════════════════╗
  ║  STEP 1 — VALIDATE                                               ║
  ╚══════════════════════════════════════════════════════════════════╝
  │
  │  • Check: Is the input field empty?
  │       YES → Show warning alert → STOP
  │
  │  • Check: Does the URL pass isValidUrl() ?
  │    (Uses native URL constructor — requires http:// or https://)
  │       NO  → Show error alert → STOP
  │       YES → Continue ↓
  │
  │  • UI Actions:
  │       - Disable scan button
  │       - Show scanning overlay
  │       - Activate Step 1 indicator
  │       - Clear terminal + results
  │       - Write "[OK] URL format validated" to terminal
  │
                                 │
                                 ▼
  ╔══════════════════════════════════════════════════════════════════╗
  ║  STEP 2 — EXIST CHECK                                            ║
  ╚══════════════════════════════════════════════════════════════════╝
  │
  │  • Sends: fetch(url, { method:'HEAD', mode:'no-cors' })
  │  • Timeout: 8 seconds via AbortController
  │
  │  OUTCOMES:
  │  ┌─────────────────────────────────────────────────────────────┐
  │  │  Fetch resolves (no throw)  →  URL EXISTS  →  Continue ↓   │
  │  ├─────────────────────────────────────────────────────────────┤
  │  │  AbortError (timeout)       →  URL UNREACHABLE             │
  │  │  "Failed to fetch"          →  URL UNREACHABLE             │
  │  │  "NetworkError"             →  URL UNREACHABLE             │
  │  │       → Show "NOT FOUND" result card                        │
  │  │       → Show error alert                                    │
  │  │       → Update stats → STOP                                 │
  │  ├─────────────────────────────────────────────────────────────┤
  │  │  Other error (e.g. CORS)    →  Assume EXISTS → Continue ↓  │
  │  └─────────────────────────────────────────────────────────────┘
  │
  │  • UI Actions:
  │       - Mark Step 1 completed (✓)
  │       - Activate Step 2 indicator
  │       - Write connectivity status to terminal
  │
                                 │
                                 ▼
  ╔══════════════════════════════════════════════════════════════════╗
  ║  STEP 3 — DEEP ANALYSIS                                          ║
  ╚══════════════════════════════════════════════════════════════════╝
  │
  │  • Mark Step 2 completed (✓), activate Step 3
  │  • Write analysis progress lines to terminal (with delays)
  │  • Call: EnhancedURLAnalyzer.analyzeUrl(url)
  │
  │  INSIDE THE ANALYZER — 7 CHECKS RUN IN SEQUENCE:
  │
  │  ┌────┬──────────────────────────────┬──────────────────────────┐
  │  │ #  │ CHECK                        │ CONDITION                │
  │  ├────┼──────────────────────────────┼──────────────────────────┤
  │  │ 1  │ Protocol Security            │ http: instead of https:  │
  │  │ 2  │ Suspicious TLD               │ .tk .ml .ga .cf .gq      │
  │  │    │                              │ .xyz .top                │
  │  │ 3  │ IP Address as Host           │ hostname = x.x.x.x       │
  │  │ 4  │ Phishing Keywords            │ secure login verify      │
  │  │    │                              │ update bank              │
  │  │ 5  │ Urgency / Scare Words        │ urgent now expire locked  │
  │  │ 6  │ @ Symbol in URL              │ @ present anywhere       │
  │  │ 7  │ Excessive Hyphens            │ 3+ hyphens in domain     │
  │  └────┴──────────────────────────────┴──────────────────────────┘
  │
  │  SCORING:
  │  • Each check adds points independently
  │  • Scores accumulate into risk_score
  │  • Final score capped at 100
  │
  │  VERDICT DECISION:
  │  ┌──────────────────────────────────────────────────────────────┐
  │  │  risk_score  0 – 34  →  SAFE                                │
  │  │  risk_score 35 – 59  →  SUSPICIOUS                          │
  │  │  risk_score 60 – 100 →  PHISHING (DANGEROUS)               │
  │  └──────────────────────────────────────────────────────────────┘
  │
  │  RETURNS:
  │  {
  │    url, risk_score, verdict,
  │    reasons[], risk_factors[], risk_categories{}
  │  }
  │
                                 │
                                 ▼
  ╔══════════════════════════════════════════════════════════════════╗
  ║  STEP 4 — RESULT DISPLAY                                         ║
  ╚══════════════════════════════════════════════════════════════════╝
  │
  │  • Hide scanning overlay
  │  • Re-enable scan button
  │  • Mark all steps completed (✓)
  │  • Call displayResults(result)
  │
  │  RENDER ACTIONS:
  │  ┌──────────────────────────────────────────────────────────────┐
  │  │  1. Apply color theme to result card                         │
  │  │     SAFE → green border + glow                               │
  │  │     SUSPICIOUS → orange border + glow                        │
  │  │     DANGEROUS → red border + pulsing glow animation          │
  │  │                                                              │
  │  │  2. Display verdict label + numeric risk score               │
  │  │                                                              │
  │  │  3. Animate risk bar from 0% → actual score%                 │
  │  │     (CSS transition: 1.5s cubic-bezier easing)               │
  │  │                                                              │
  │  │  4. Render factor breakdown list                             │
  │  │     Each factor → type / description / score badge           │
  │  │                                                              │
  │  │  5. Write summary to terminal log                            │
  │  │                                                              │
  │  │  6. Show toast alert (color-coded by verdict)                │
  │  │                                                              │
  │  │  7. Update session statistics                                │
  │  └──────────────────────────────────────────────────────────────┘
  │
                                 │
                                 ▼
  ┌─────────────────────────────────────────────────────────────────────┐
  │  SCAN COMPLETE — User reads verdict and factor breakdown            │
  └─────────────────────────────────────────────────────────────────────┘


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  4. FULL DATA FLOW DIAGRAM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  [User Input: URL string]
          │
          ▼
  [isValidUrl()] ──── INVALID ────▶ [Alert: "Invalid URL"] ──▶ END
          │
        VALID
          │
          ▼
  [checkUrlExists()] ── UNREACHABLE ──▶ [displayUrlNotExist()] ──▶ END
          │                                      │
        EXISTS                           [updateStats()] ──▶ END
          │
          ▼
  [EnhancedURLAnalyzer.analyzeUrl()]
          │
          ├── Check 1: Protocol      → +15 if HTTP
          ├── Check 2: TLD           → +25 if suspicious
          ├── Check 3: IP Host       → +30 if IP address
          ├── Check 4: Keywords      → +8 per phishing word
          ├── Check 5: Urgency       → +6 per urgency word
          ├── Check 6: @ Symbol      → +30 if present
          └── Check 7: Hyphens       → +15 if 3+ hyphens
                    │
                    ▼
          [risk_score = sum (capped at 100)]
                    │
          ┌─────────┴──────────┐
          │                    │
        0–34                35–59              60–100
       SAFE              SUSPICIOUS           PHISHING
          │                    │                  │
          └────────────────────┴──────────────────┘
                               │
                               ▼
                    [displayResults()]
                               │
                    ┌──────────┴──────────┐
                    │                     │
              Result Card           Terminal Log
              Risk Bar              Toast Alert
              Factor List           Stats Update


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  5. COMPONENT MAP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ┌─────────────────────────────────────────────────────────────────────┐
  │  COMPONENT           │ TYPE       │ ROLE                           │
  ├──────────────────────┼────────────┼────────────────────────────────┤
  │  .grid-bg            │ CSS/HTML   │ Animated background grid       │
  │  .particles          │ JS + CSS   │ 50 floating dot elements       │
  │  .alert-message      │ JS + CSS   │ Slide-in toast notifications   │
  │  .scanning-overlay   │ JS + CSS   │ Full-screen scan animation     │
  │  .progress-steps     │ JS + CSS   │ 4-step progress indicator      │
  │  .scanner-card       │ HTML/CSS   │ Main input + output container  │
  │  .url-input          │ HTML       │ URL text input field           │
  │  .scan-btn           │ HTML/JS    │ Triggers startScan()           │
  │  .terminal-output    │ JS + CSS   │ Live scan log display          │
  │  .result-card        │ JS + CSS   │ Verdict + risk bar + details   │
  │  .stats-row          │ JS + CSS   │ Session counter cards          │
  │  EnhancedURLAnalyzer │ JS Class   │ Core threat scoring engine     │
  │  CONFIG              │ JS Object  │ Keyword + TLD configuration    │
  │  stats               │ JS Object  │ In-memory session counters     │
  └──────────────────────┴────────────┴────────────────────────────────┘


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  6. STATE MACHINE — SCAN LIFECYCLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  IDLE
    │
    │  User clicks Scan
    ▼
  VALIDATING ──── fail ────▶ IDLE (alert shown)
    │
    │  pass
    ▼
  CHECKING EXISTENCE ──── unreachable ────▶ NOT_FOUND (result shown)
    │
    │  reachable
    ▼
  ANALYZING
    │
    │  complete
    ▼
  RENDERING RESULT
    │
    │  done
    ▼
  IDLE (ready for next scan)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  7. SCORING ENGINE — DETAILED RULES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ┌────┬──────────────────────┬──────────────────────────────┬────────┐
  │ #  │ RULE NAME            │ DETECTION LOGIC              │ SCORE  │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 1  │ Insecure Protocol    │ parsed.protocol === 'http:'  │  +15   │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 2  │ Suspicious TLD       │ domain ends with:            │  +25   │
  │    │                      │ tk ml ga cf gq xyz top       │        │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 3  │ IP Address Host      │ /^\d+\.\d+\.\d+\.\d+$/      │  +30   │
  │    │                      │ matches hostname             │        │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 4  │ Phishing Keywords    │ URL contains any of:         │ +8 ea  │
  │    │                      │ secure login verify          │        │
  │    │                      │ update bank                  │        │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 5  │ Urgency Keywords     │ URL contains any of:         │ +6 ea  │
  │    │                      │ urgent now expire locked     │        │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 6  │ @ Symbol             │ url.includes('@')            │  +30   │
  ├────┼──────────────────────┼──────────────────────────────┼────────┤
  │ 7  │ Excessive Hyphens    │ domain hyphen count >= 3     │  +15   │
  └────┴──────────────────────┴──────────────────────────────┴────────┘

  MAX POSSIBLE SCORE (theoretical):
  15 + 25 + 30 + (5×8) + (4×6) + 30 + 15 = 179 → capped at 100


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  8. UI ANIMATION SYSTEM
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ANIMATION           │ TRIGGER              │ MECHANISM
  ────────────────────┼──────────────────────┼──────────────────────────
  Grid background     │ Always on            │ CSS @keyframes gridMove
  Floating particles  │ Page load            │ JS DOM + CSS float anim
  Header fade-in      │ Page load            │ CSS fadeInDown
  Scanner card fade   │ Page load            │ CSS fadeInUp
  Scan line sweep     │ Always on            │ CSS scanTop keyframe
  Overlay rings spin  │ During scan          │ CSS spin keyframe
  Step indicators     │ Each scan step       │ JS class toggle
  Risk bar fill       │ Result display       │ CSS width transition
  Danger card pulse   │ DANGEROUS verdict    │ CSS dangerPulse keyframe
  Toast slide-in      │ After each scan      │ CSS translateX transition
  Stats count-up      │ After each scan      │ JS requestAnimationFrame
  Shake animation     │ NOT FOUND result     │ CSS shake keyframe


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  9. ERROR HANDLING MATRIX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ERROR CONDITION          │ DETECTION              │ USER RESPONSE
  ─────────────────────────┼────────────────────────┼──────────────────────
  Empty input              │ url.length === 0        │ Warning toast
  Invalid URL format       │ URL constructor throws  │ Error toast
  DNS failure / no domain  │ "Failed to fetch"       │ NOT FOUND card
  Request timeout (>8s)    │ AbortError              │ NOT FOUND card
  Network error            │ "NetworkError"          │ NOT FOUND card
  CORS block               │ Other fetch error       │ Assume exists, proceed
  URL parse error in engine│ try/catch in analyzer   │ Error toast + terminal


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  10. CONFIGURATION REFERENCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  All tunable parameters live in the CONFIG object at the top of
  the <script> block:

  const CONFIG = {
      SUSPICIOUS_TLDS:    ['tk','ml','ga','cf','gq','xyz','top'],
      PHISHING_KEYWORDS:  ['secure','login','verify','update','bank'],
      URGENCY_KEYWORDS:   ['urgent','now','expire','locked']
  };

  To extend detection coverage, simply add more entries to these arrays.
  No other code changes are needed — the scoring engine reads from CONFIG
  dynamically.

  OTHER TUNABLE VALUES:
  ┌──────────────────────────────┬──────────────────────────────────────┐
  │  Fetch timeout               │  8000ms (AbortController delay)      │
  │  SAFE threshold              │  score < 35                          │
  │  SUSPICIOUS threshold        │  score 35–59                         │
  │  DANGEROUS threshold         │  score >= 60                         │
  │  Alert auto-dismiss          │  5000ms (setTimeout in showAlert)    │
  │  Risk bar animation speed    │  1.5s CSS transition                 │
  │  Particle count              │  50 (initParticles loop)             │
  │  Stats animation duration    │  1000ms (animateValue)               │
  └──────────────────────────────┴──────────────────────────────────────┘


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  11. DESIGN DECISIONS & RATIONALE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  DECISION                   │ RATIONALE
  ───────────────────────────┼──────────────────────────────────────────
  Single HTML file           │ Zero setup — open and run anywhere
  No ML model                │ Rule-based = explainable + instant
  no-cors fetch mode         │ Only way to probe cross-origin URLs
                             │ from a browser without a proxy server
  Async scan with delays     │ UX — gives user time to read terminal
  Seeded stats (1247 scans)  │ Demo realism — shows what live data
                             │ looks like without needing real history
  Score capped at 100        │ Consistent 0–100 scale for the risk bar
  CSS-only animations        │ No JS animation library needed
  CONFIG object at top       │ Easy to extend without touching logic


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Built by AI Alchemist  |  SHIELD SCAN System Design v1.0

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
