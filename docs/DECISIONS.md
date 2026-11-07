# Architectural decisions and open questions

## Accepted direction
- **D-001:** TeleMIDI Band is a separate consumer application, not a MIDIHub module.
- **D-002:** Hybrid design: MIDI for initial instruments, audio for vocals, with optional audio-only and dual-stream instruments.
- **D-003:** Every member receives the others and controls an individual monitor mix.
- **D-004:** Local monitoring must not depend on a network round trip.
- **D-005:** Initial client is native AROS; standards-based protocol interoperability is a design constraint, but building Linux/Windows/macOS clients is not an initial deliverable.
- **D-006:** First validation is a **two-AROS-peer** hybrid LAN demo, not an Internet-scale product.
- **D-007:** No duplication of native CAMD/MIDIHub/Network MIDI/mDNS infrastructure.
- **D-008:** Hybrid MIDI/audio is not claimed to be unique; competitive differentiation through MIDI 2.0 fidelity, native integration and musician-centered workflow must be validated with evidence.

See [competitive analysis](COMPETITIVE_ANALYSIS.md), [integration contract](INTEGRATION_CONTRACT.md), [session protocol](SESSION_PROTOCOL.md), [timing plan](TIMING_AND_LATENCY.md), and [validation plan](VALIDATION_PLAN.md).

## Pending decisions
- **Q-001:** Does AHI provide adequate full-duplex audio capture/playback and latency on target hardware?
- **Q-002:** Is AOO portable to AROS with acceptable dependency and maintenance cost?
- **Q-003:** What Opus settings and buffering minimize musician-to-musician delay?
- **Q-004:** What clock mapping and scheduling can reliably correlate MIDI events with audio frames?
- **Q-005:** How should MIDI instrument programs/SoundFonts be shared or matched?
- **Q-006:** Which topology scales best to 4+ musicians: P2P, relay or server-side mixer?
- **Q-007:** What rendezvous/NAT traversal and encryption strategy is practical for Internet mode?
- **Q-008:** Which license should the application use, considering possible GPL components?
- **Q-009:** What minimal UI toolkit is feasible natively on AROS?
- **Q-010:** Which platform MIDI API contracts are stable enough for the application adapter?

Record each resolution with evidence, implementation references and tradeoffs; avoid marking candidate technologies as adopted prematurely.
