# Roadmap — ordered by dependency

No stage below should be marked complete without executable evidence.

## G0 — Contract and capability audit (P0)
- Inventory actual CAMD 43, MIDIHub, Network MIDI, mDNS/DNS-SD and AHI APIs available in the intended AROS build.
- Verify MIDI 1/UMP support, endpoint identity, timestamps, synthesis, **soundbank catalog/load/preset APIs**, audio duplex, and event notifications.
- Define the verified native AROS integration contract and identify protocol-level interoperability requirements without requiring a Linux client.
- Record API gaps upstream in their owning repositories, not as duplicate implementations here.
**Exit:** capability matrix with code locations, test plan and named blockers.

## G1 — Audio feasibility (P0)
- Audit AOO, libopus and their transitive dependencies against AROS compilers, threads, sockets, timers and AHI.
- Build an AROS-native capture/playback loopback and measure round-trip audio latency.
- Prototype Opus encode/decode and a 2-peer audio transport with adjustable jitter.
**Exit:** reproducible build and measured duplex voice path; document fallback if AOO is impractical.

## G2 — Two-peer hybrid LAN demo (P0)
- **Two AROS peers**; one MIDI controller and microphone on each.
- Native platform MIDI transport and routing; separate low-latency voice stream.
- Negotiate instrument manifests and match local soundbanks by hash; explicitly report fallback and source-audio options (asset transfer may be staged after core demo).
- Both participants hear remote MIDI and voice; adjustable independent return levels.
- Local direct monitor and no accidental network feedback loops.
- Telemetry: latency, jitter, drift, loss, CPU, underruns and stuck-note recovery.
**Exit:** repeatable bidirectional demonstration and measurements.

## G3 — Four-person band LAN (P1)
- Drums, bass, keys/guitar MIDI and voice; instrument profiles and independent mixes.
- Participant join/leave, reconnection, clock handling, source attribution and multitrack recording.
- Implement permissioned soundbank transfer/cache where justified; handle non-shareable/hardware instruments via source audio or labeled approximation.
**Exit:** every musician hears the other three and can adjust their own monitor mix.

## G4 — Internet rehearsal (P1, feasibility-gated)
- Session rendezvous, authentication, NAT traversal or relay, encryption and adaptive buffers.
- Evaluate P2P vs relay topology under realistic RTT/jitter/loss.
**Exit:** measured regional trials with clearly stated usable latency envelope.

## G5 — Collaborative studio mode (P2)
- Asynchronous overdubs, separate tracks, timeline alignment and export.
- Recovery of sessions and deterministic offline mix.
**Exit:** remotely recorded, editable multi-part song.

## Cross-cutting acceptance artifacts
- [Native integration contract](INTEGRATION_CONTRACT.md) and source-linked G0 capability matrix.
- [User experience](USER_EXPERIENCE.md) and [session protocol requirements](SESSION_PROTOCOL.md).
- [Timing and latency plan](TIMING_AND_LATENCY.md) and [validation matrix](VALIDATION_PLAN.md).
- [Competitive evidence](COMPETITIVE_ANALYSIS.md) for feature prioritization, not unsupported exclusivity claims.
- Test interoperability with an external standards-compliant peer after native AROS viability is demonstrated; no cross-platform client implementation required for G2.

## Engineering rules
- Prefer thin platform adapters and integration tests over replacing existing infrastructure.
- Track feature maturity as planned / prototyped / validated / upstreamed.
- Benchmark with explicit hardware, network, sample rate, codec settings and buffer sizes.
- No hard real-time or zero-latency claims without evidence.
