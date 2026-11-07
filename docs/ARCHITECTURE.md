# Architecture — initial direction

## Core principle
TeleMIDI Band is an **application-layer consumer** of existing AROS services. MIDI transport, CAMD 43, MIDIHub routing, MIDI-CI, discovery, and synthesis remain owned by their respective infrastructure components.

## Per-peer pipeline

```text
Local MIDI device --> CAMD / MIDIHub --> MIDI session adapter ---> remote peers
Local microphone --> AROS audio capture --> audio encoder -------> remote peers

Remote MIDI streams --> CAMD / MIDIHub --> local synthesizer --+
Remote audio streams --> decoder ------------------------------+--> personal mixer --> headphones
Local direct monitor ------------------------------------------+
```

This is a logical data-flow, not a claim about implemented AROS APIs.

## Application components
1. **Band/session coordinator:** members, roles, authorization, invitation, peer state and recovery.
2. **MIDI adapter:** consume native MIDI endpoints and route streams through supported platform interfaces; avoid duplicate protocol stacks.
3. **Audio adapter:** full-duplex capture/playback, codec, transport, jitter control and device lifecycle.
4. **Timing coordinator:** timestamp correlation, clock drift, playback scheduling, metrics and diagnostics.
5. **Personal monitor mixer:** independent levels/pan/mute for every remote source; local sidetone/direct monitoring.
6. **Multitrack recorder:** source attribution, separate MIDI/audio tracks, offline alignment and export.
7. **UI:** band room, membership, instrument assignment, transport health, individual monitor mix.

## Separation of concerns
- **MIDIHub:** platform-level device management, routing and services. TeleMIDI Band requests routes/capabilities instead of implementing them.
- **CAMD:** MIDI events/UMP and endpoint-facing contracts.
- **AROSTCP + DNS-SD:** network primitives and local discovery; not application-level identity or Internet rendezvous.
- **AHI:** candidate native audio I/O; verify duplex, timestamps, buffering and usable latency.
- **AOO / Opus:** candidate audio-over-network stack and codec, **not yet selected or ported**.

## Transport topology tradeoffs
- **P2P:** direct individual streams; bandwidth grows with participant count; NAT traversal required over Internet.
- **Relay/SFU-like:** forwards streams without decoding/mixing; simplifies connectivity, preserves per-peer mix, adds hop latency.
- **Server-side audio mixer:** reduces downstream streams but complicates individual mixes and changes latency.
- Benchmark a 2-peer LAN case before choosing a topology for 4+ peers.

## Timing and correctness
- Keep local monitoring on-device; remote return must not echo the musician's own instrument unless explicitly selected.
- Treat MIDI event time and audio sample time as distinct clock domains; estimate offsets and drift.
- Account for synth rendering latency, codec frame size, jitter buffers and audio I/O.
- Distinguish transport latency from *musician-to-musician audible* latency.
- Ensure note-off/all-notes-off handling on disconnection; avoid stuck notes.
- Consider instrument program/SoundFont compatibility so peers hear comparable timbres.
- Support MIDI guitar/MPE when supported by endpoints; MIDI guitar does not carry the original guitar tone.
- Secure room admission, authenticate peers and protect voice streams before Internet rollout.

## Out of scope for first PoC
Social feeds, video, public discovery, payments, mobile clients, cloud production services and perfect Internet-wide live synchronization.
