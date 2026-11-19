# Reference implementations and research targets

These are **study candidates**, not selected dependencies or claims of AROS compatibility. Audit upstream version, licenses, transitive dependencies and maintenance before vendoring or porting.

| Project | URL | Why inspect |
| --- | --- | --- |
| SonoBus | https://github.com/sonosaurus/sonobus | Individual monitor mixes, collaborative audio UX, latency/jitter controls |
| AOO (Audio over OSC) | https://github.com/Spacechild1/aoo | Reusable real-time network audio, timing, buffering and transport |
| Jamulus | https://github.com/jamulussoftware/jamulus | Centralized low-latency audio sessions and mixer tradeoffs |
| Opus | https://github.com/xiph/opus | Portable audio codec candidate for voice and instruments |
| libremidi | https://github.com/celtera/libremidi | Cross-platform MIDI application API and interoperability design |
| rtpmidid | https://github.com/davidmoreno/rtpmidid | External RTP-MIDI interoperability peer |
| VMPK | https://github.com/pedrolcl/VMPK | Virtual keyboard and MIDI UI reference |
| Open Piano | https://github.com/ja-ortiz-uniandes/open-piano | Collaborative visual instrument UX |
| TeleMIDI Connect | https://devcandoit.com/portfolio/telemidi-connect | Browser-based collaborative MIDI concept |
| AROS MIDIHub | https://github.com/JJDSNT/aros-midihub | Primary AROS MIDI integration dependency |
| AROS | https://github.com/aros-development-team/AROS | Native CAMD, audio and networking source |

## MIDI 2.0 application-level validation reference

| Product | Reference | Research and validation focus |
| --- | --- | --- |
| MultitrackStudio | https://midi.org/multitrackstudio-brings-practical-midi-2-0-support-to-audio-and-midi-recording | Independent DAW reference for MIDI 2.0 UMP/per-note expression, MIDI-CI Profiles/Property Exchange, multitrack recording and MIDI 2.0 file interchange; confirm exact version, available features and usable connection path before testing |

MultitrackStudio is a **feature benchmark and potential interoperability peer**, not an AROS dependency, open-source implementation, network-jam reference or proof of compatibility. It must not drive a separate MIDI stack in TeleMIDI Band. Native CAMD/platform MIDI services own MIDI-CI, UMP and transport; TeleMIDI Band owns recording, participant attribution and session behavior. See [validation plan](VALIDATION_PLAN.md) for proposed checks and evidence requirements.

## Investigation checklist
- Identify exact upstream license and compatible reuse boundaries.
- Confirm source accessibility and build requirements.
- Compare C/C++ standards, atomics, threading, timing, sockets and audio I/O assumptions with AROS.
- Find code-level seams for platform adapters, especially AHI and CAMD.
- Document what is reusable as a library versus what is only a design reference.
- Avoid using an application's embedded MIDI transport in place of the native MIDIHub stack.

## Competitive products and experience references

These products are **feature benchmarks**, not verified reusable source code. See [competitive analysis](COMPETITIVE_ANALYSIS.md) for evidence limits and the distinction between confirmed and unconfirmed capabilities.

| Product | Reference | Research focus |
| --- | --- | --- |
| Muse Sessions | https://musesessions.co/ | Hybrid MIDI/audio sessions, DAW integration, recording, latency limitations |
| Play4Me | https://play4.me/ | Browser-based collaborative instruments, MIDI/audio workflow and recording |
| Wire Chord | https://wirechord.com/ | MIDI-event exchange, local synthesis, jam-room UX and hybrid audio/voice claims |
| MIDIverse | https://midiverse.io/ | P2P MIDI collaboration, shared clock and small-group sessions; verify current availability |
| SonoBus | https://sonobus.net/ | Individual monitor mixes, participant audio UX and latency controls |

## Protocols, specifications and supporting implementations

| Reference | URL | Why inspect |
| --- | --- | --- |
| MIDI-CI specification | https://midi.org/midi-ci-specification | Capability discovery, profiles and property exchange |
| MIDI 2.0 overview | https://midi.org/midi-2-0 | UMP, per-note expressivity and backward compatibility; check precise spec documents |
| AOO upstream | https://git.iem.at/aoo/aoo | Canonical implementation and documentation; compare with GitHub mirror |
| SonoBus AOO fork | https://github.com/essej/aoo | Historical embedded fork; **not** the recommended basis for a new AOO integration |
| Opus codec | https://opus-codec.org/ | Codec reference, frame sizes, delay and interoperability |
| RTP-MIDI / AppleMIDI | https://www.rfc-editor.org/rfc/rfc6295 | Standard network MIDI transport reference |
| MIDI Polyphonic Expression | https://midi.org/mpe-midi-polyphonic-expression | MIDI 1.0 expressivity comparison; distinguish from native MIDI 2.0 per-note controls |

## Research traceability

- [Competitive analysis](COMPETITIVE_ANALYSIS.md): confirmed claims, gaps, candidate differentiators and comparison scenarios.
- [Integration contract](INTEGRATION_CONTRACT.md): native AROS owners, unverified APIs and audit requirements.
- [Timing and latency](TIMING_AND_LATENCY.md): clock domains, audio/MIDI synchronization and measurement plan.
- [Validation plan](VALIDATION_PLAN.md): reproducible acceptance tests.

**Evidence discipline:** links alone do not establish feature support, portability, licensing or source-code availability. Before selecting a dependency, record an upstream revision, license, specific source files, test results and target AROS architecture. Vendor marketing claims must not be reported as measured performance.

## Distributed music collaboration: deeper research (2026-11-18)

These are **architectural and experimental references**, not adopted dependencies or demonstrated AROS ports. Evaluate source, licenses, maturity, reproducible tests and platform seams before choosing any implementation.

| Initiative | Source / documentation | Verified or documented behavior | What TeleMIDI Band should investigate | Caveat |
| --- | --- | --- | --- | --- |
| netpd | https://github.com/reduzent/netpd ; https://netpd.org/docs/protocol/ ; https://github.com/reduzent/netpd-server | Pure Data instruments and parameters synchronized among clients; OSC 1.1 over TCP with SLIP framing, relay server | Instrument/session state snapshots, late join, participant-origin identification, convergence after reconnect | Not evidence of low-latency MIDI transport; TCP head-of-line blocking differs from time-critical event delivery; GPL-2.0 |
| Fast MIDI Jam | https://github.com/serifpersia/fast-midi-jam | C++ UDP MIDI client/server; external server/UDP port-forwarding or tunnel described | Inspect `client.cpp`, `server.cpp`, `midi_utils.cpp` for event framing, note lifecycle, packet validation, timing and loss behavior | **Archived 2026-02-19**; no MIDI 2.0, timing, security or reliability claim accepted without source-level tests |
| NINJAM | https://cockos.org/ninjam/ ; https://github.com/justinfrankel/ninjam | Source-available interval-based network audio collaboration; session material can be imported for editing | Musically delayed / bar-aligned alternative to immediate interaction, interval boundaries, recordings and participant UX | Audio-centric; not a MIDI 2.0 or CAMD implementation; review component licenses |
| AOO | https://git.iem.at/aoo/aoo ; https://github.com/Spacechild1/aoo | C/C++ audio streaming with sample-accurate timestamped messages, adjustable jitter, drift compensation, Opus/PCM, source/sink topology | Whether audio frames and MIDI events can share an explicitly mapped timeline; feasibility of thin AROS adapter and tests | Upstream calls itself alpha; **do not use** the historical SonoBus-specific `essej/aoo` fork as the new library basis |
| SonoBus | https://github.com/sonosaurus/sonobus | Per-user monitor controls, adjustable jitter buffer, multitrack recording | Personal mixes, diagnostics and recording UX | Audio-first application, not a native MIDI 2.0 implementation |
| Jamulus | https://github.com/jamulussoftware/jamulus | Server receives and mixes client audio, sends mix back | Compare server-side mixing, bandwidth, individual mix limitations and latency | Different topology from event-first/local-synthesis approach |
| Meshroom | https://github.com/janiluuk/meshroom | MIDI Sync / Ableton Link bridge, up to four collaborators, latency indicators, recording/session manifests | Beat/tempo alignment, sync modes, quantization controls, per-participant tracks, recovery metadata | Early development, browser/Node/Docker/LiveKit stack; not an AROS portability candidate by default |
| PianoJam | https://github.com/Halfaxas/PianoJam | Browser MIDI keyboards, multiplayer rooms, locally synthesized instruments, synchronized metronome | Room lifecycle, per-player instrument choice, note-off cleanup and UX | Socket.IO/browser architecture; WAV recording does not imply MIDI 2.0 event export |
| ptcollab | https://github.com/yuxshao/ptcollab | Collaborative piano-roll sequencing and session replay | Asynchronous collaborative composition, event-history replay, edit/record distinction | Sequencer, not a network performance/UMP reference |
| OpenStudio | https://github.com/braedonsaunders/openstudio ; https://github.com/braedonsaunders/openstudio/blob/main/docs/native-bridge-roadmap.md | Browser collaboration, synchronized backing tracks, optional Rust native bridge | Separate local device latency from network transport, performer/listener roles, relay-vs-P2P design | Full deployment requires Supabase, Cloudflare Calls/R2; performance and roadmap claims require independent measurements |
| Telemidi / TEMPO (closed reference) | https://telemidi.com/tempo-protocol/ ; https://midi.org/telemidi-creating-music-over-the-internet-in-real-time ; https://ro.ecu.edu.au/theses/2862/ | Describes timestamped MIDI, clock skew calibration, latency-accepting musical techniques and audience reconstruction | Compare performer experience versus reconstructed audience timeline; formulate independent experiments | Patent-pending proprietary protocol; no implementation reuse or claimed 3 ms performance without independent evidence |

### Cross-reference priorities

1. **State and ownership:** netpd + PianoJam: separate reliable membership/instrument configuration from real-time note events.
2. **Hybrid timebase:** AOO + Meshroom: inspect timestamps, clock domains, tempo/beat phase and audio frame correlation; do not assume clocks are interchangeable.
3. **Musically acceptable delay:** NINJAM + Telemidi research: benchmark immediate, bounded-jitter and bar/beat-quantized modes separately.
4. **Record and reconstruct:** ptcollab + Meshroom + SonoBus: retain original event time, receive time, source identity, local render offsets and optional audience timeline.
5. **Network topology and ergonomics:** Jamulus + OpenStudio: compare direct, relay and mixed-audio topologies; distinguish marketed latency from measured end-to-end audible latency.

**Do not infer** native MIDI 2.0/UMP or MIDI-CI support from generic MIDI, MIDI clock, Web MIDI or Ableton Link mentions. Do not vendor GPL source or proprietary protocol material without explicit license and architectural review. Preserve AROS platform ownership of CAMD/MIDIHub/transport/MIDI-CI.
