# GeoTruth
GeoTruth is an open-source Python engine for environmental fraud detection. It verifies real-world conditions using multi-modal signals like audio, motion, and geo-data, enabling reliable parametric claim validation across insurance, mobility, logistics, and climate systems.

# 🛡️ Adversarial Defense & Anti-Spoofing Strategy

> **Crisis Response — Phase 1 Update | GigShield | DEVTrails 2026**
> Triggered by: Market Crash Scenario — 500-worker GPS Spoofing Syndicate

---

## The Threat We're Solving

A coordinated ring of 500 delivery workers organized via Telegram exploited GPS spoofing apps to fake their location inside red-alert weather zones — triggering mass false parametric payouts and draining the liquidity pool.

Simple GPS verification is **officially obsolete.**

GigShield's response is not a patch. It is a complete rearchitecting of how presence and disruption are **verified** — introducing our proprietary fraud detection framework:

---

## Introducing: **GeoTruth™ Engine**
### *Multi-Modal Environmental Coherence Verification (MECV)*

> **GeoTruth** is GigShield's proprietary, open-sourceable Python fraud detection module — purpose-built for parametric insurance claim verification. Unlike identity fraud tools (Seon, Kount, Telesign) that detect *who* is filing, GeoTruth detects *whether the claimed physical environment is real*.

```bash
# Our open-source package — being built as part of this project
pip install geotruth
```

**Core Philosophy:** GPS tells you coordinates. GeoTruth tells you whether those coordinates are *lived reality or a digital lie.*

GeoTruth cross-validates **7 independent passive signal channels** against the claimed disruption event — making simultaneous spoofing across all channels statistically impossible without physical presence.

---

## Part 1: Differentiating Genuine vs. Bad Actor — The 7-Layer Coherence Stack

A genuine delivery partner caught in a Chennai flood and a fraud actor spoofing from their bedroom generate **fundamentally different signal patterns.** GeoTruth detects this divergence.

### Layer 1 — Barometric Pressure Coherence (Novel)
**The insight:** Every modern Android phone has a barometric pressure sensor. During severe weather events — cyclones, heavy rainfall, storms — atmospheric pressure drops measurably and hyperlocally. This sensor *cannot* be spoofed by a GPS spoofing app.

```
Genuine Worker:           Phone barometer reads 990–1000 hPa (low pressure storm front)
                          Matches nearest IMD weather station reading ±3 hPa ✅

Fraud Actor at Home:      Phone barometer reads 1012–1015 hPa (normal indoor conditions)
                          Does NOT match claimed severe weather zone ❌
```

**GeoTruth Action:** Query device barometer → cross-reference with IMD real-time pressure grid → compute Pressure Coherence Score (0–1).

---

### Layer 2 — Passive Acoustic Environment Fingerprinting (Novel)
**The insight:** Rain, high winds, flood water, and street chaos have a measurable acoustic signature. GeoTruth uses the phone microphone passively (opt-in at onboarding, disclosed in T&C) to extract a 3-second ambient audio fingerprint when a claim is auto-initiated.

```
Signal Profile — Genuine Heavy Rain Zone:
  └── Low-frequency rumble: 80–200 Hz (rain impact on surfaces)
  └── White noise envelope: 500–4000 Hz (rainfall density)
  └── Human speech absent (outdoor, alone)

Signal Profile — Home / Indoor Fraud:
  └── Flat frequency response (ambient silence or TV)
  └── No rain signature
  └── Possible conversation audio
```

Audio is **never stored or transmitted** — only the extracted acoustic feature vector (14 floats) is sent. Fully privacy-preserving.

**GeoTruth Action:** Run lightweight TensorFlow Lite rain/weather classifier (Edge ML) on-device → output Acoustic Coherence Flag.

---

### Layer 3 — Network Topology Truth (Beyond GPS)
**The insight:** A GPS spoofing app changes your reported coordinates. It does NOT change which cell towers your phone is connected to, which WiFi SSIDs are visible, or your IP geolocation. These form a "Network Truth Fingerprint."

```
Genuine Worker in Field (e.g., Koramangala, Bengaluru):
  └── Cell tower IDs: 3–5 towers from Jio/Airtel mapped to that neighborhood ✅
  └── WiFi probe list: Mix of commercial SSIDs (coffee shops, restaurants)
  └── IP geolocation: Resolves to the claimed zone (within 500m) ✅

Fraud Actor at Home (Spoofing to Koramangala):
  └── GPS coordinates: Koramangala ← SPOOFED
  └── Cell tower IDs: Towers from home neighborhood (Marathahalli?) ❌
  └── WiFi probe list: Home router SSIDs (previously seen = residential) ❌
  └── IP geolocation: Home broadband location ❌
```

**GeoTruth Action:** Collect passive network fingerprint → run against a pre-trained cell-tower geographic map (built from OpenCelliD + TRAI data) → compute Network Topology Coherence Score.

---

### Layer 4 — Inertial Motion Signature
**The insight:** A delivery partner caught in a flood is moving — sheltering under an awning, retreating to higher ground, pushing their bike. A fraud actor sitting at home is stationary or has normalized motion (walking around their flat).

GeoTruth analyzes **accelerometer + gyroscope patterns** in the 30 minutes *before* and *during* the claim:

```
Genuine Stranded Partner:
  └── Irregular high-variability motion before event (active delivery)
  └── Sudden cessation / erratic motion during event (seeking shelter)
  └── Low-frequency micro-vibrations consistent with rain on device

Fraud Actor:
  └── Stationary baseline (at home, sitting)
  └── Sudden movement spike when spoofing app is activated (picked up phone)
  └── Stationary again immediately after
```

**GeoTruth Action:** Inertial pattern classifier → outputs Motion Coherence Score + "Suspiciously Activated" flag.

---

### Layer 5 — Platform Behavioral Correlation
**The insight:** If a disruption is real, ALL workers in that zone stop getting orders simultaneously. GigShield has access to platform activity signals. A genuine disruption creates a *zone-wide activity cliff* visible across all enrolled workers in that pincode.

```
Genuine Zone Disruption:
  └── 15+ enrolled workers in pincode show order volume drop simultaneously ✅
  └── App heartbeat data shows all devices still online (workers not sleeping)

Isolated Fraud Claim:
  └── Only 1–3 workers from that zone file claims
  └── Other workers in same pincode remain active with normal order flow ❌
```

**GeoTruth Action:** Zone coherence check → if >70% of active workers in zone show disruption signal, boost trust score. If lone claim with active neighbors, flag immediately.

---

### Layer 6 — Historical Behavioral Baseline (Personalized)
Every enrolled worker builds a **Personal Trust Profile** over 4 weeks:

- Typical work hours (when they're usually active)
- Standard delivery zones (their home turf)
- Normal phone usage patterns during deliveries
- Historical claim frequency vs. disruption frequency

```python
# GeoTruth computes deviation from personal baseline
baseline_deviation_score = geotruth.compute_deviation(
    worker_id="W-2947",
    current_claim_vector=[...],
    personal_baseline=worker_profile.behavioral_baseline
)
# A worker who suddenly files a claim at 2 AM in a zone they've never
# delivered in before scores high on deviation → elevated suspicion
```

---

### Layer 7 — Social Coordination Graph Detection (Ring Detector)
**The insight:** Coordinated fraud rings show temporal clustering. Fraudsters in a Telegram group receive the instruction "spoof to Zone X now" → 500 claims arrive within minutes. This is statistically impossible for genuine stranded workers.

```
Genuine Disruption Claim Pattern:
  └── Claims arrive gradually over 20–40 minutes (workers find shelter, then file)
  └── Geographic spread matches the weather system's actual footprint

Coordinated Fraud Ring Pattern:
  └── Burst of claims within 3–5 minutes (Telegram signal received)
  └── Geographic pinpoints cluster unusually (all spoofed to same "hot" zone)
  └── Workers who have NEVER interacted in physical space all file simultaneously
  └── Device fingerprints show same spoofing app signature (same OS hook patterns)
```

**GeoTruth Action:** Build a real-time claim arrival graph → run temporal burst detection → identify suspicious claim clusters → apply Social Coordination Score.

**If 20+ claims arrive for the same pincode within 4 minutes when historical avg is 8 claims/hour → RING ALERT → freeze zone payouts → trigger human review.**

---

## Part 2: Data Points Beyond GPS — The GeoTruth Feature Vector

GeoTruth computes a **38-dimensional feature vector** per claim. Here is the complete manifest:

### Environmental Signals (Device-Sourced)
| Feature | Source | Cannot Be Spoofed By |
|---|---|---|
| Barometric pressure reading | Phone sensor | GPS spoofing apps |
| Pressure delta vs. IMD grid | Sensor + API | Any standard app |
| Acoustic rain/wind signature | Microphone (edge inference) | GPS + location faking |
| Ambient light level (indoor vs outdoor) | Light sensor | GPS apps |
| Device temperature (outdoor exposure) | Battery temp sensor | GPS apps |

### Network Signals
| Feature | Source | Cannot Be Spoofed By |
|---|---|---|
| Cell tower IDs (3 nearest) | Telephony API | GPS apps |
| Cell tower signal strength pattern | Telephony API | GPS apps |
| WiFi SSID probe list (passive scan) | WiFi API | GPS apps |
| WiFi SSID vs home router hash | WiFi API + profile | GPS apps |
| IP geolocation vs claimed GPS | IP lookup | GPS apps |
| Connection type (4G/5G/WiFi) | Network API | GPS apps |

### Motion & Temporal Signals
| Feature | Source | Cannot Be Spoofed By |
|---|---|---|
| Pre-claim 30min accelerometer variance | IMU sensor | GPS apps |
| Gyroscope pattern (stationary vs sheltering) | IMU sensor | GPS apps |
| Screen unlock pattern before claim | Device API | GPS apps |
| App activation timestamp (spoofing app?) | Device telemetry | Basic GPS tools |

### Social & Zone Signals
| Feature | Source | Detects |
|---|---|---|
| Zone-wide order volume delta | Platform API | Fake isolated claims |
| Claim arrival burst rate (zone) | GigShield DB | Telegram ring coordination |
| Worker cluster density in zone | GigShield DB | Hot-zone stuffing |
| Personal baseline deviation score | Historical profile | Behavior anomalies |
| Inter-claim time similarity | GigShield DB | Coordinated timing |

### Cross-Validation Signals
| Feature | Source | Detects |
|---|---|---|
| IMD weather alert vs. claimed zone | IMD RSS | No actual weather event |
| AQICN AQI vs. claimed AQI event | AQICN API | Fake pollution claims |
| 3-source weather consensus score | OWM + IMD + Windy | Single-source manipulation |
| Pincode disruption prevalence | GigShield DB | Isolated vs. widespread event |

---

## Part 3: UX Balance — Handling Flagged Claims Without Penalizing Honest Workers

This is the hardest design challenge: **fraud detection must not become a poverty trap.**

A genuine delivery partner in Chennai, caught in a flood, with a cheap ₹6,000 Redmi phone on 2G connectivity, may trigger several false positives simply because:
- Their GPS signal is weak and jumpy (looks like spoofing)
- Their phone has no barometer sensor (budget Android)
- Their network dropped during the event (no real-time data upload possible)

GigShield's UX solution is the **Grace-First Workflow:**

---

### Tier 0 — Auto-Approved (No Friction)
**GeoTruth Coherence Score: 0–35 (Low Suspicion)**

✅ Payout triggered immediately — worker does nothing.
- Time to payout: < 8 minutes
- No notification of any review
- Worker experience: "₹800 credited. Stay safe 🙏"

---

### Tier 1 — Passive Enrichment (Zero Worker Burden)
**GeoTruth Coherence Score: 36–55 (Moderate Suspicion)**

The system quietly collects *additional passive signals* for 15 minutes without bothering the worker at all. No action required on their part.

During this 15-minute window:
- GeoTruth re-polls network topology (did cell towers change? → they moved = genuine)
- Checks if neighboring workers also went offline (zone-wide = genuine)
- Waits for IMD alert to officially cover their pincode

**If passive enrichment resolves suspicion → auto-approve. Worker felt nothing.**
- Time to payout: 15–25 minutes
- Worker sees: "Your claim is being processed 🔄" (no mention of flag)

---

### Tier 2 — Soft Proof Request (Non-Punitive)
**GeoTruth Coherence Score: 56–74 (Elevated Suspicion)**

Worker receives a **single, non-accusatory push notification** (vernacular: Tamil/Hindi/Telugu) with one of three optional soft-proof actions:

```
📸 Option A: "Tap to share a 5-second video of your surroundings"
   → Rain, waterlogging, or blocked route visible → score drops 30 points → approved

📍 Option B: "Share your live location for 10 minutes"  
   → Cell-tower confirmed location matches claim → approved

💬 Option C: "Reply with the name of the area you're in"
   → Basic geo-challenge → verified against claimed zone
```

**Key Design Principle:** The worker has 45 minutes to respond. If they don't respond (because they're in a genuine emergency with no connectivity), the system defaults to **benefit of the doubt** if zone-level data supports the disruption.

**No response + zone confirms disruption = auto-approved after 45 minutes.**
**No response + zone shows no disruption = held for review, not denied.**

- Time to payout: 30–60 minutes (or 8 min if proof submitted)
- Worker sees: "Help us confirm your location to speed up your claim 🙏" (no "fraud" language)

---

### Tier 3 — Human Review Queue (Not Auto-Denied)
**GeoTruth Coherence Score: 75–100 (High Suspicion)**

The claim enters a human review queue. Critically:

**The worker receives an advance partial payout of ₹200 immediately** — a "trust signal" that the platform believes them and is reviewing, not punishing.

```
Worker Notification:
"We're doing a quick check on your claim (this helps us protect 
all partners from fraud). You'll receive the full amount within 
4 hours. We've sent ₹200 as an advance. 🙏"
```

The human reviewer sees the full GeoTruth signal breakdown — not just a "fraud score" — so they can make a contextual decision. A worker whose barometer was flagged because they have a ₹5,000 phone without that sensor gets a **sensor-absent grace flag** which instructs reviewers to discount that signal entirely.

**Outcome SLA:** Human review must complete within 4 hours. If reviewer approves → remaining payout sent. If denied → ₹200 advance is retained (not clawed back from worker).

---

### Tier 4 — Fraud Confirmed (Ring Member Detected)
**GeoTruth Social Coordination Score: RING ALERT**

If a worker is positively identified as part of a coordinated ring:
1. All pending claims are frozen
2. Policy is suspended (not cancelled — pending investigation)
3. Worker is notified and given 7 days to dispute via a documented appeal process
4. Dispute reviewed by a 3-person panel (not the same reviewer who flagged them)

**Appeal guarantee:** Every flagged worker has the right to appeal. False positives in genuine disasters will be overturned with full payout + ₹50 inconvenience credit.

---

## GeoTruth™ — Technical Architecture Summary

```
                    ┌─────────────────────────────────┐
                    │        CLAIM INITIATED           │
                    │   (Auto or Worker Triggered)     │
                    └────────────┬────────────────────┘
                                 │
              ┌──────────────────▼──────────────────┐
              │         GeoTruth Engine v1.0         │
              │   Multi-Modal Coherence Verifier     │
              └──────────────────┬──────────────────┘
                                 │
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
  ┌─────────────┐       ┌──────────────┐       ┌──────────────────┐
  │ DEVICE      │       │ NETWORK      │       │ SOCIAL/ZONE      │
  │ SENSOR PACK │       │ TOPOLOGY     │       │ GRAPH DETECTOR   │
  │             │       │ VALIDATOR    │       │                  │
  │ - Barometer │       │ - Cell Tower │       │ - Burst Detector │
  │ - Acoustic  │       │ - WiFi SSID  │       │ - Ring Analyzer  │
  │ - IMU/Motion│       │ - IP Geo     │       │ - Zone Coherence │
  │ - Light/Temp│       │ - Conn. Type │       │ - Platform Delta │
  └──────┬──────┘       └──────┬───────┘       └──────┬───────────┘
         │                     │                       │
         └─────────────────────▼───────────────────────┘
                                │
                    ┌───────────▼──────────┐
                    │  38-Feature Vector   │
                    │  Fusion & Scoring    │
                    │  (XGBoost Ensemble)  │
                    └───────────┬──────────┘
                                │
                    ┌───────────▼──────────┐
                    │  Coherence Score     │
                    │  0 ─────────── 100   │
                    └───────────┬──────────┘
                                │
            ┌───────────────────┼──────────────────────┐
            ▼                   ▼                      ▼
     Score 0–35          Score 36–74            Score 75–100
   AUTO APPROVE      GRACE WORKFLOW          RING DETECTION
   (< 8 minutes)    (passive + soft          (freeze + appeal
                     proof request)           + advance pay)
```

---

## Why GeoTruth™ Is Architecturally Novel

| Existing Tool | What It Does | What It Misses |
|---|---|---|
| Telesign / Seon | Identity fraud (is this person real?) | Physical presence verification |
| Kount / Stripe Radar | Payment fraud patterns | Environmental coherence |
| Basic GPS Verification | Coordinate check | Everything a spoofing app bypasses |
| Standard Isolation Forest | Anomaly detection on transactions | Coordinated ring topology detection |
| **GeoTruth™** | **Multi-modal physical presence coherence** | **Nothing — this is the gap we fill** |

**What GeoTruth introduces that no existing package does:**
1. **Parametric-specific feature engineering** — designed for insurance triggers, not transactions
2. **Device sensor vs. environment API coherence** — cross-validates hardware sensors with external data
3. **Social graph temporal burst detection** — purpose-built for Telegram-coordinated ring attacks
4. **Budget device sensor fallback logic** — degrades gracefully for sub-₹8,000 phones without premium sensors
5. **Grace-first UX scoring** — fraud score is not binary; it drives a human-centered workflow ladder

---

## Open Source Roadmap: `geotruth` Python Package

```python
# Proposed package interface
from geotruth import GeoTruthEngine, ClaimVector, WorkerProfile

engine = GeoTruthEngine(config={
    "imd_api_key": "...",
    "opencellid_token": "...",
    "sensor_weights": "parametric_v1"
})

claim_vector = ClaimVector(
    worker_id="W-2947",
    claimed_pincode="600001",
    timestamp=1711900000,
    device_barometer_hpa=991.2,
    acoustic_feature_vector=[...],  # 14-dim, computed on-device
    cell_tower_ids=["404-20-12345", "404-20-12346"],
    wifi_ssid_hashes=["a3f2...", "9c1b..."],
    accelerometer_variance_30m=0.847,
    screen_unlock_count_1h=3
)

result = engine.verify(claim_vector, worker_profile=WorkerProfile.load("W-2947"))

# result.coherence_score     → 0–100
# result.tier                → "auto_approve" | "passive_enrich" | "soft_proof" | "human_review" | "ring_alert"
# result.flagged_signals     → ["cell_tower_mismatch", "acoustic_no_rain"]
# result.sensor_gaps         → ["barometer_absent"]  ← used to apply grace discounts
# result.recommendation      → "APPROVE" | "SOFT_PROOF" | "REVIEW" | "FREEZE"
```

**Package will be released on PyPI post-hackathon as an open-source contribution to the parametric insurance fintech ecosystem.**

---

## Summary: Why This Strategy Wins

| Requirement | GigShield Response |
|---|---|
| Differentiate genuine vs. spoofing | 7-layer coherence stack — GPS is just 1 of 38 signals |
| Go beyond GPS + platform logs | Barometric pressure, acoustics, cell towers, IMU, social graph |
| Don't penalize honest workers | Grace-first 5-tier workflow; advance payout even for flagged claims; sensor-gap grace flags |
| Detect coordinated rings | Temporal burst detector + social coordination graph |
| Technical innovation | GeoTruth™ — first open-source multi-modal parametric claim verifier |

> *GigShield doesn't just detect fraud. It protects the honest.*

---
*Section added: March 2026 | In response to Market Crash Scenario — Phase 1 Update*
*GeoTruth™ is a proprietary concept developed by the GigShield team for DEVTrails 2026*

