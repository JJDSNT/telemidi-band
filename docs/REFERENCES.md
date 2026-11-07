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
