Keystroke ID

A working prototype of behavioral biometric authentication — it checks not just what password you type, but how you type it.

What it does

Most logins only check what you typed. This checks how you typed it. Two people can know the same password, but the timing of how they press each key — how long they hold it, how long they pause between keys — is much harder to copy. That timing pattern is called a keystroke dynamics signature, and this project captures it, learns it, and uses it to make access decisions in real time, entirely in the browser.

Features


Enrollment — type your chosen password 5 times to build a personal timing profile (dwell time per key, flight time between keys)
Authentication — re-type the same password; the app compares your live timing against your enrolled profile using cosine similarity and returns a confidence score
Adaptive drift tracking — successful logins slightly update your baseline over time (your rhythm naturally drifts day to day), while a session log shows the score history so a sudden shift is easy to spot
Visual fingerprint — your timing profile rendered as a radial chart, with your enrolled baseline and most recent attempt overlaid for direct comparison


How it works


Capture — keydown/keyup listeners record millisecond-precision timestamps via performance.now() for every key in the password field
Feature extraction — for each attempt, the app computes:

Dwell time: how long each key is held down
Flight time: the gap between releasing one key and pressing the next



Profile building — the 5 enrollment samples are averaged per key/gap into a baseline vector
Matching — each login attempt's vector is compared to the baseline using cosine similarity, producing a 0–100% confidence score

≥ 82% → unlock
60–82% → borderline, asks for re-entry
< 60% → blocked, even if the password text is correct



Drift adaptation — on a successful unlock, the baseline is nudged toward the new sample (exponential moving average, 85/15 weighting) so natural changes in typing speed don't lock out the real user over time


Tech notes


Single HTML file, no build step, no backend
Pure JavaScript timing capture (no external biometrics library)
Profile and attempt history persist across sessions via the artifact storage API
Matching uses cosine similarity over a combined dwell + flight feature vector — a lightweight, explainable stand-in for the ML models production keystroke-dynamics systems use


Limitations (be upfront about these)


This is a browser-based demo, not an OS-level login replacement — it can't hook into your actual laptop's lock screen, which would require kernel/driver-level access
Typing rhythm naturally varies with tiredness, mood, and typing surface, so false rejections are a known tradeoff in real keystroke-dynamics systems, not just this prototype
Cosine similarity on raw timings is a simplified stand-in for the statistical/ML models (e.g. Gaussian models, SVMs) that production systems like TypingDNA or BehavioSec use


Why this exists

Built as a portfolio project to demonstrate behavioral biometrics concepts: real-time signal capture, feature engineering from raw event timing, similarity-based verification, and adaptive baselining — the same building blocks used in production passwordless-auth systems, scoped down to something a single HTML file can demonstrate honestly.
