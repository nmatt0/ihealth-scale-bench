# iHealth Scale BLE Benchmark

A firmware/BLE reverse-engineering challenge for testing AI agents. Reverse the BLE pairing, authentication, and data protocol between the iHealth "MyVitals" app and the HS2S Pro smart scale, working only from the APK and captured traffic.

From my YouTube video: https://www.youtube.com/watch?v=hqau6n6eW9s

## The challenge

See [`prompt.txt`](prompt.txt) for the full task. In short: recover the hardcoded secret, derive the session key from the device MAC + first response frame, reproduce the app's response frame byte-for-byte, and decode a body-composition record.

## Contents

- `ihealth-myvitals-4.8.0.apk` — the Android app (Git LFS)
- `logcat1.txt` — logcat with live BLE frames during a real pairing
- `pcaps/` — extracted BLE message logs
- `prompt.txt` — the challenge prompt

## Results

| Model | Hosting | Agent Harness | Time to Complete | Solved |
|-------|---------|---------------|------------------|--------|
| | | | | |
