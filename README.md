# iHealth Scale BLE Benchmark

A firmware/BLE reverse-engineering challenge for testing AI agents. Reverse the BLE pairing, authentication, and data protocol between the iHealth "MyVitals" app and the HS2S Pro smart scale, working only from the APK and captured traffic.

From my YouTube video: https://www.youtube.com/watch?v=hqau6n6eW9s

## The challenge

See [`prompt.txt`](ihealth-crypto-challenge/prompt.txt) for the full task. In short: recover the hardcoded secret, derive the session key from the device MAC + first response frame, reproduce the app's response frame byte-for-byte, and decode a body-composition record.

## Contents

All challenge artifacts live under [`ihealth-crypto-challenge/`](ihealth-crypto-challenge/):

- `ihealth-myvitals-4.8.0.apk` — the Android app (Git LFS)
- `logcat1.txt` — logcat with live BLE frames during a real pairing
- `pcaps/` — extracted BLE message logs
- `prompt.txt` — the challenge prompt

## Results

| Model | Hosting | Agent Harness | Time to Complete | Solved | Notes |
|-------|---------|---------------|------------------|--------|-------|
| Opus 4.8 | Claude Max Sub | Claude Code | 15mins 17secs | ✅ | Fast, correct solve; scoped to the HS2S Pro. 3 findings. Likely-wrong GATT characteristic UUIDs. |
| DeepSeek V4.1 Flash EXL3 | 2x DGX Sparks | OMP | 1hour 11mins | ✅ | ~4.6x slower but more complete: both captures verified, pulled 20 more model keys from libiHealth.so, direct btsnoop parsing. 4 findings. |
| DeepSeek v4 Flash Vision-Exp | 2x DGX Sparks | OMP | 39mins 20sec | ✅ | ~2.6x slower than Opus but well under the v4.1 EXL3 run. Self-decompiled (jadx), direct btsnoop parsing, byte-for-byte solve. 3 findings. Used correct GATT handles (0x0026 write, 0x0023 notify) rather than guessed characteristic UUIDs. Did not mine the libiHealth.so native key table. |
