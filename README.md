# Smile_Please
This is our readme file for DevTrails Phase I 2026. We have come up with an offline video recording app implemented using geotag, timestamps, and anti-AI features. Each upload will trigger a unique prompt for the user to perform in order to rule out as many frauds as possible.

Adversarial Defense & Anti-Spoofing Strategy

Core principle: No single signal is foolproof — but making a fraudster defeat all four layers simultaneously in real time is practically impossible.

**1. THE DIFFERENTIATION**
The pillars below are ordered by when they execute in the pipeline — from the moment the worker opens the claim screen to the moment our server processes it:
        
**Pillar 1** — Liveness Video with Randomized Prompt

        •	Worker records a 3-5 second video responding to a randomized in-app prompt 
                generated fresh at submission time.
                
        •	Example prompts: Slowly pan left / Hold up 3 fingers / Say today's date out loud.
        
        •	Prompt is randomized at submission — a pre-recorded video cannot anticipate it.
        
        •	An AI-generated image or deepfake cannot respond to a live, real-time instruction.
        
**Pillar 2** — Mock Location Detection

        •	App checks Android's isMockLocationEnabled flag at the exact moment of submission.
        
        •	If a GPS spoofing app is active, the claim is rejected immediately, before any                      server resources are spent.
        
        •	Limitation: rooted devices can hide this flag. However, rooting requires                            significant technical knowledge, well beyond most syndicate participants.
        
        •	Eliminates the majority of unsophisticated fraud at zero computational cost.        
        
**Pillar 3** — Multi-Source Location Fusion
        •	GPS coordinates: the primary declared location.
        
        •	Wi-Fi positioning: nearby Wi-Fi networks visible to the device, cross-referenced                   against OpenCelliD / Google Geolocation API.
        
        •	Cell tower ID: resolved to an approximate location via OpenCelliD
        
        A spoofer can fake GPS via software, but not which Wi-Fi networks are physically visible to         their device and which cell tower their SIM is connected to, as both are determined by              physical proximity.
        
**Pillar 4** — AI-Generated Media Detection
        •	EXIF metadata validation — real camera captures contain rich metadata (lens info,                   sensor data, exposure). AI-generated images have missing or inconsistent metadata.
        
        •	AI detection models — submissions run through CNNDetection or Microsoft Content                     Credentials to flag synthetic media with high accuracy.



        
