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
| DeepSeek v4 Flash Vision-Exp | OpenRouter | OMP | 22mins 15sec | ✅ | Fastest DeepSeek run and the most complete verification: proved the 0xFC frame byte-for-byte on all 4 sessions, decoded 4 offline records (both read1 and read2's distinct records), and recovered the real characteristic UUIDs (sed./rec.jiuan.HS2S02) not just handles. 3 findings. Did not pull the libiHealth.so native key table. Listed all 9 Java model keys but 3 mis-transcribed from jadx named constants (target HS2S Pro key correct). |
| GLM 5.3 Flash | 2x DGX Sparks | OMP | 57min 3s | ✅ | Correct solve, all 4 sessions byte-for-byte. Most findings (5) but severity over-scored (weak-PRNG rated High 8.3 vs Low elsewhere). Cross-checked smali to nail the target key. Decoded only 1 record; 2 sibling keys mis-transcribed from jadx named constants. |
| GLM 5.3 Flash | OpenRouter | OMP | 32min 33sec | ✅ | Cleanest GLM run: solved all 4 sessions byte-for-byte, first run with all 9 Java model keys correct (smali-verified). Real char UUIDs (rec./sed.jiuan). 5 findings incl. logcat key-material disclosure (CWE-532). But CVSS attack vector wrong on headline finding (AV:N rates it Critical 9.1; a BLE-proximity attack is AV:A, ~8.1 High). Decoded read2's 3 records; no native key table. |
| Qwen3.8 Flash Next | 2x DGX Sparks | OMP | 1hour 13mins | ✅ | Slowest run of the set, ~1.85x slower than DeepSeek v4 Flash on the same Sparks. Correct solve, all 9 model keys right, real char UUIDs, 7 findings with correct AV:A vectors. But two report claims contradict its own PoC: XXTEA labeled big-endian (code is little-endian) and the record timestamp quotes the session time (0x66C63996) not the decoded measure_time, then "confirms" it. Verified read2, skipped read1. |
