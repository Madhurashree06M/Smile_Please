# Smile_Please
This is our readme file for DevTrails Phase I 2026. We have come up with an offline video recording app implemented using geotag, timestamps, and anti-AI features. Each upload will trigger a unique prompt for the user to perform in order to rule out as many frauds as possible.

Adversarial Defense & Anti-Spoofing Strategy
This section outlines our platform's approach to detecting and preventing coordinated GPS-spoofing fraud by delivery partners. Our strategy is built on a single core principle: no single signal is foolproof, but making a fraudster defeat all layers simultaneously — at scale, in real time — is practically impossible.
The four pillars below are ordered by when they execute in the claim submission pipeline — from the moment the worker opens the claim screen to the moment our server processes it.
1. The Differentiation
How our AI/ML architecture differentiates between a genuinely stranded delivery partner and a bad actor spoofing their location.
A genuine delivery partner in a severe weather zone produces a consistent, multi-signal profile that a fraudster cannot replicate from home. Our system evaluates four independent verification layers at claim submission time. A fraudster must defeat all four simultaneously — in real time — to successfully trigger a false payout.
Pillar 1 — Liveness Video with Randomized Prompt
Instead of a static photo, the worker must record a short 3–5 second video responding to a randomized in-app instruction generated at the moment of submission. Examples include:
•	"Slowly pan left"
•	"Hold up three fingers"
•	"Say today's date out loud"
Why this works:
•	The prompt is randomized at submission time — a pre-recorded video cannot anticipate it.
•	An AI-generated image or deepfake cannot respond to a live, real-time instruction.
•	This mechanism is directly borrowed from banking KYC systems — battle-tested at scale.
Pillar 2 — Mock Location Detection
Before any media is processed, the app checks Android's isMockLocationEnabled flag at the exact moment of claim submission. If a GPS spoofing app is active on the device, the claim is rejected immediately — before any server resources are spent evaluating it.
Limitations and mitigation:
•	Rooted Android devices can hide this flag. However, rooting requires significant technical knowledge — well beyond what most syndicate participants would have.
•	This layer acts as a first-pass filter eliminating the majority of unsophisticated fraud attempts at zero cost. The remaining layers handle the sophisticated minority.
Pillar 3 — Multi-Source Location Fusion
At the exact moment the worker submits their claim, our app simultaneously captures three independent location signals:
1.	GPS coordinates — the primary declared location.
2.	Wi-Fi positioning — the physical locations of Wi-Fi networks visible to the device, cross-referenced against public databases (Google Maps Geolocation API / OpenCelliD). A fraudster at home will see their home router and neighborhood networks — placing them in a residential area, not a flood zone.
3.	Cell tower ID — the unique ID of the tower the device is connected to, resolved against OpenCelliD to get an approximate location. Compared against the worker's historical tower pattern from past deliveries. A worker who has always operated in Whitefield suddenly claiming to be in Koramangala is flagged.
Why this works:
A spoofer can fake GPS coordinates via software. They cannot simultaneously fake which physical Wi-Fi networks are visible to their device and which cell tower their SIM is connected to — both are determined by physical proximity, not software. If all three signals contradict each other, the claim is flagged for review.
Pillar 4 — AI-Generated Media Detection
Every submitted video is passed through a detection pipeline on our server before the claim is processed:
•	EXIF metadata validation — real camera captures contain rich metadata (lens info, sensor data, exposure settings). AI-generated images have missing or inconsistent metadata.
•	Noise pattern analysis — physical camera sensors produce a unique noise fingerprint (PRNU). AI-generated media has statistically different noise characteristics detectable algorithmically.
•	AI detection models — submissions are run through models such as CNNDetection or Microsoft Content Credentials to flag synthetically generated media with high accuracy.
2. The Data
Specific data points our system analyzes beyond basic GPS coordinates.
Device-Level Signals (captured at submission)
•	isMockLocationEnabled flag — Android OS API, no permissions or partnerships required.
•	Nearby Wi-Fi network identifiers — visible networks physically surrounding the device.
•	Connected cell tower ID — resolved via OpenCelliD to approximate location, no carrier partnership needed.
Weather Verification
•	IMD (India Meteorological Department) API cross-validation — the system verifies that an active, government-declared red alert genuinely exists at the claimed GPS coordinates at the time of submission. The parametric trigger event must be real and current.
•	This prevents fraudsters from filing claims during periods of no real weather event, and ensures the trigger itself cannot be fabricated.
Historical Behavioral Signals (platform's own data)
•	Operating zone history — the worker's habitual delivery zones from past activity. A claim from a zone the worker has never operated in is flagged.
•	Cell tower history — the worker's historical tower ID pattern from past sessions. A sudden tower mismatch on claim day is flagged.
•	Claim frequency — first-time vs. repeat claimant risk scoring. Workers with a pattern of claims on every weather alert are escalated for review.
3. The UX Balance
How our workflow handles flagged claims without unfairly penalizing honest workers.
We reject the binary of instant approval vs. hard denial. Flagged claims enter a Tiered Verification Workflow designed to be frictionless for honest workers and costly for bad actors. Critically, no worker is permanently penalized based solely on an algorithmic flag — every blocked claim has a clear human appeals path.
Tier 1 — Auto-Approved
All four signals are coherent and pass. Payout triggers immediately. Zero friction for workers with clean profiles. This is the expected path for the vast majority of legitimate claims.
Tier 2 — Soft Hold (90 minutes)
One or more signals are borderline but not conclusive. Payout is held for 90 minutes. The worker receives a friendly in-app notification:
"We're verifying conditions in your area — this is automatic and will not affect your status. You'll hear back shortly."
During this window the system passively re-evaluates signals. If the worker moves or conditions update, the payout auto-releases. No human involvement required in most cases.
Tier 3 — Active Verification
Multiple signals are low but not conclusive. The worker is prompted to complete the liveness video check-in if not already submitted, or to provide a brief location re-confirmation. This is low effort for a genuinely stranded worker and practically impossible to fake at scale. Workers are explicitly told this does not affect their standing.
Tier 4 — Fraud Hold
High-confidence fraud signals detected. Payout is blocked. The worker is notified of a delay (not the fraud suspicion) and given a clear appeals path with a dedicated support contact. The claim is queued for human review within 24 hours. Appeal outcomes feed back into the model to reduce future false positives.
Key design principle:
A genuine worker with a broken device, poor connectivity, or an unusual work location should never permanently lose access to the platform. The tiered system ensures speed for the honest majority while creating enough friction to make large-scale coordinated fraud economically unviable.
4. Offline Resilience
A legitimate worker stranded in a severe weather zone may have degraded or no connectivity — the very conditions that triggered their claim. Our app uses an offline-first architecture to handle this:
•	The liveness video, Wi-Fi snapshot, and cell tower ID are captured and locked locally on the device when connectivity is unavailable.
•	All signals are timestamped at capture time on the server — not at upload time. A 2-hour delay in uploading due to poor signal does not invalidate the claim.
•	This also adds a fraud detection layer: a fraudster would need to have been physically present at the flood zone at capture time to produce valid geotagged, multi-signal data — which defeats the entire purpose of the fraud.
