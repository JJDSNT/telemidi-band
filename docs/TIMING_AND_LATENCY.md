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
