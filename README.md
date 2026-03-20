# Smile_Please

This is our README file for DevTrails Phase I 2026.  
We have come up with an offline video recording app implemented using geotag, timestamps, and anti-AI features.  

Each upload will trigger a unique prompt for the user to perform in order to rule out as many frauds as possible.

---

## Adversarial Defense & Anti-Spoofing Strategy

**Core principle:**  
No single signal is foolproof — but making a fraudster defeat all four layers simultaneously in real time is practically impossible.

---

## 1. THE DIFFERENTIATION

The pillars below are ordered by when they execute in the pipeline — from the moment the worker opens the claim screen to the moment our server processes it:

---

### Pillar 1 — Liveness Video with Randomized Prompt

- Worker records a **3–5 second video** responding to a randomized in-app prompt generated fresh at submission time.  
- Example prompts:
  - Slowly pan left  
  - Hold up 3 fingers  
  - Say today’s date out loud  
- Prompt is randomized at submission — a pre-recorded video cannot anticipate it.  
- An AI-generated image or deepfake cannot respond to a live, real-time instruction.

---

### Pillar 2 — Mock Location Detection

- App checks Android's `isMockLocationEnabled` flag at the exact moment of submission.  
- If a GPS spoofing app is active, the claim is **rejected immediately**, before any server resources are spent.  
- **Limitation:** rooted devices can hide this flag.  
- However, rooting requires significant technical knowledge, well beyond most syndicate participants.  
- Eliminates the majority of unsophisticated fraud at zero computational cost.

---

### Pillar 3 — Multi-Source Location Fusion

- GPS coordinates → primary declared location  
- Wi-Fi positioning → nearby networks cross-referenced with OpenCellID / Google Geolocation API  
- Cell tower ID → approximate location via OpenCellID  

A spoofer can fake GPS via software, but **cannot fake:**
- Which Wi-Fi networks are physically visible  
- Which cell tower their SIM is connected to  

Both are determined by physical proximity.

---

### Pillar 4 — AI-Generated Media Detection

- EXIF metadata validation:
  - Real camera captures contain rich metadata (lens info, sensor data, exposure)
  - AI-generated images have missing or inconsistent metadata  
- AI detection models:
  - Submissions run through CNNDetection or Microsoft Content Credentials  
  - Flags synthetic media with high accuracy

  ---

## 2. The Data

Every signal we collect beyond basic GPS coordinates — and why we chose it.

---

### 🔹 Data Collection Pipeline

```text
        ┌────────────────────────┐
        │   User Submission      │
        │ (Video + Location)     │
        └──────────┬─────────────┘
                   │
        ┌──────────▼─────────────┐
        │  Device-Level Signals  │
        │                        │
        │  • Mock Location Flag  │
        │  • Wi-Fi Networks      │
        │  • Cell Tower ID       │
        └──────────┬─────────────┘
                   │
        ┌──────────▼─────────────┐
        │  Location Validation   │
        │                        │
        │  • GPS Coordinates     │
        │  • Wi-Fi Cross-check   │
        │  • Cell Tower Mapping  │
        └──────────┬─────────────┘
                   │
        ┌──────────▼─────────────┐
        │  Weather Verification  │
        │                        │
        │  • IMD API Check       │
        │  • Red Alert Validation│
        └──────────┬─────────────┘
                   │
        ┌──────────▼─────────────┐
        │   Fraud Decision       │
        │                        │
        │  ✔ Accept / Reject     │
        └────────────────────────┘
```
---

## 3. The UX Balance

*How flagged claims are handled without penalizing honest workers.*

We reject the binary of instant approval vs. hard denial. Every flagged claim enters a four-tier workflow.  

No worker is ever permanently penalized based solely on an algorithmic flag — every blocked claim has a clear human appeals path.

---

### 🔹 Claim Handling Workflow

| Tier | Status | Description |
|------|--------|------------|
| **Tier 1** | 🟢 **Auto-Approved** | All signals pass. Payout triggers immediately. Zero friction. Expected path for the vast majority of legitimate claims. |
| **Tier 2** | 🟡 **Soft Hold** | Borderline signals. Payout held for 90 minutes. Worker notified: *"We're verifying conditions — this won't affect your status."* Auto-releases if signals resolve. No human needed in most cases. |
| **Tier 3** | 🟠 **Active Check** | Multiple low signals. Worker prompted to complete liveness check-in or re-confirm location. Low effort for genuine worker; nearly impossible to fake at scale. Workers told explicitly this does not affect their standing. |
| **Tier 4** | 🔴 **Fraud Hold** | High-confidence fraud. Payout blocked. Worker notified of delay *(not fraud suspicion)* and given a clear appeals path. Human review within 24 hours. Outcomes feed back into the model to reduce future false positives. |

---

### 🔹 Key Principle

> A genuine worker with a broken device, poor connectivity, or an unusual work location should **never permanently lose access** to the platform.  
> **Speed for the honest majority. Friction for bad actors.**

---

---

## 4. Offline Resilience

*Because the worst connectivity is always in the worst weather.*

A legitimately stranded worker may have no signal — the exact conditions that triggered their claim.  
Our **offline-first architecture** is designed to handle this seamlessly:

---

### How It Works

- **Local Signal Capture**  
  - Liveness video, Wi-Fi snapshot, and cell tower ID are captured and securely stored on the device when connectivity is unavailable  

- **True Timestamping**  
  - All signals are timestamped at **capture time — not upload time**  
  - A delay (even hours) due to poor signal does **not invalidate the claim**  

- **Tamper Resistance**  
  - Signals are **locked locally once captured**  
  - Upload delay actually **strengthens trust** by preventing post-hoc manipulation  
---

### 🔹 Key Insight

> Lack of connectivity is not a failure case — it is often **the strongest indicator of a legitimate claim**.

---
