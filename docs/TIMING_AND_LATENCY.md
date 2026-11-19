# Timing, latency and synchronization plan

## Distinct clock domains
1. MIDI event timestamps / MIDI transport timing
2. Audio capture and playback sample clocks
3. Monotonic host clock
4. Remote peer clocks
5. Optional session/recording timeline

A mapping (offset + drift estimate + uncertainty) is needed before events and audio frames can be aligned. Do not equate wall-clock timestamps with monotonic scheduling time.

## End-to-end latency budget
- MIDI path: input capture -> timestamp -> network send -> network receive/jitter buffer -> synth scheduling/render -> audio output.
- Voice path: capture buffer -> codec frame/encode -> network -> jitter buffer -> decode -> playback buffer.
- Local monitor: local input -> local audio path, excluding network transit.

Measure both instrument-to-audible-output and musician-to-musician audible delay. Report median, p95/p99, maximum, dropouts, buffer sizes, sample rates and test environment. No unsupported fixed latency promise.

## Experiments
- AHI full-duplex loopback on target AROS hardware; determine sample rates and buffer limits.
- Two-peer LAN with simultaneous MIDI and voice; log RTT and, where synchronized clocks exist, one-way delay.
- Jitter/loss/reordering injection; evaluate audible glitches and stuck notes.
- Clock drift over long sessions; monitor rebuffering and note timing.
- Local monitoring and echo/feedback prevention.
- Four-person scenario with independent synthesis and mixing CPU load.

## Engineering decisions to defer
AOO clock model and transport, codec frame size, buffer adaptation, tempo/beat-aware synchronization versus true live playing, and P2P versus relay. A jitter buffer trades responsiveness for stability; no software can eliminate propagation delay.

## Acceptance evidence
Reproducible harness, build information, target architecture, timestamp logs, objective metrics and short listening-test protocol. Define acceptable musical interaction thresholds only after measurement.

## Instrument assets and real-time isolation
Soundbank discovery, hashing, authorization, download, verification and synth initialization must happen outside the real-time MIDI/audio callback path. Measure instrument-switch readiness separately from performance latency. Preserve note-off cleanup and avoid discontinuities on renderer changes.

## Research-derived experiment directions (2026-11-18)

Sources: [netpd OSC/TCP protocol](https://netpd.org/docs/protocol/), [AOO upstream](https://github.com/Spacechild1/aoo), [NINJAM](https://cockos.org/ninjam/), [Meshroom](https://github.com/janiluuk/meshroom), and [Telemidi TEMPO research](https://telemidi.com/tempo-protocol/). These motivate experiments; they do not establish AROS results or endorse proprietary protocol replication.

### Separate performance modes instead of promising one universal latency

- **Immediate interaction:** timestamp at capture, locally monitor without network loop, deliver remote events promptly subject to bounded jitter and note safety. Measure musician-to-musician *audible* delay.
- **Musical-grid/latency-accepting interaction:** optional beat/bar-aligned scheduling for suitable loop- and pulse-based music. Measure missed deadlines, musical phase and responsiveness. This is not a transparent replacement for unconstrained live improvisation.
- **Offline/audience reconstruction:** retain event-time provenance and compensate known offsets after capture to reconstruct a coherent playback timeline. This can improve recording alignment but **cannot retroactively fix what musicians heard during performance**.

### Clock mapping contract to investigate

For every MIDI event or audio frame retain source/participant ID, source-local monotonic capture timestamp, source clock domain/epoch, session sequence, mapped session timestamp **with uncertainty**, receiver arrival timestamp and actual scheduled/rendered timestamp where observable. Preserve raw original values; do not rewrite source timestamps to conceal jitter. Maintain offset/drift estimates and record resets, clock discontinuities and mapping confidence. Compare audio sample counters with host monotonic time through explicit mapping, not direct equality.

### Experiment matrix

| Experiment | What to vary | Measure / failure conditions |
| --- | --- | --- |
| T01 immediate MIDI | RTT, jitter, reorder, loss, simultaneous notes | Capture-to-remote-audible delay, p50/p95/p99, missed deadlines, stuck notes |
| T02 beat/bar mode | BPM, subdivision, late events, clock drift | Quantization delay, beat-phase error, musical continuity, late-event policy |
| T03 audio/MIDI correlation | AOO-like timestamped messages versus separate MIDI/audio transport | Drift, cross-media offset, rebuffer events; avoid assuming same clock |
| T04 late join/reconnect | Session snapshot, concurrent program changes, missing note-off | State convergence, idempotent resync, note ownership and cleanup |
| T05 recorded/audience timeline | Original vs arrival vs playback timestamps, asymmetric latency | Track alignment, event ordering, reproducible reconstruction |
| T06 topology | P2P, forwarding relay, central audio mix; 2 and 4 peers | Per-user bandwidth, CPU, latency, jitter, monitor independence |
| T07 local synth fidelity | Same/different banks, program changes, loading mid-note | Sound mismatch, readiness, render delay, callback blocking |

**Design constraint:** use reliable ordered delivery where needed for room/configuration state, but evaluate time-critical MIDI delivery independently. netpd's OSC-over-TCP is a model for consistent state, not evidence that TCP is ideal for live note transport. AOO is an audio/timestamping candidate, not a replacement for native CAMD or MIDIHub transport. No performance threshold or technology is accepted until the corresponding experiment is reproducible on AROS.
