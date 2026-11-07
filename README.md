# TeleMIDI Band

**Distributed music collaboration, powered by native MIDI and low-latency audio.**

TeleMIDI Band is an experimental, open-source-oriented application concept for musicians to rehearse, perform and record together across computers and networks. Instruments initially exchange **MIDI events** while vocals (and optionally acoustic instruments) use **audio streams**. Every participant hears an individually configurable mix of the other musicians, while monitoring their own instrument locally.

**Primary platform:** AROS. **Interoperability goal:** Linux, Windows and macOS peers. The project is currently in **architecture and feasibility research**, not a working implementation.

## Product goals

- Multi-member bands: drums, bass, keyboard, MIDI guitar, vocals and other instruments.
- Full bidirectional feedback: every musician hears the others, with independent volume, pan and mute.
- Hybrid MIDI + voice/audio; allow MIDI-only, audio-only and dual-stream instruments.
- Low-latency local monitoring; avoid sending a musician's own monitor feed over the network and back.
- LAN rehearsal first, then Internet sessions; asynchronous multitrack collaboration as a separate mode.
- Record MIDI events and audio as separate tracks, preserving timing and source attribution.
- Cross-platform interoperability without requiring other musicians to run AROS.

## Architectural rule

**TeleMIDI Band is a consumer of AROS infrastructure, not a new MIDI stack.** It must not reimplement CAMD, MIDIHub routing, MIDI transports, MIDI-CI or discovery that the platform already provides.

| Concern | Intended owner |
| --- | --- |
| Band identity, rooms, membership, roles, UI, per-user mix, recording | TeleMIDI Band |
| MIDI endpoints, MIDI 1.0 / MIDI 2.0 UMP, capabilities | CAMD 43 and platform MIDI services |
| MIDI routing, synthesis and supported MIDI transports | AROS MIDIHub and related platform components |
| Local service discovery | mDNS / DNS-SD provided by AROS |
| Network connectivity | AROSTCP / bsdsocket.library |
| Local audio capture/playback | AHI or another validated native AROS audio interface |
| Remote voice/audio stream | Reusable audio library/transport, subject to feasibility audit |
| Cross-media session timing | Application coordination, using platform timing where available |

The table describes **intended integration points**, not verified, production-ready APIs. The project must document actual supported interfaces before implementing adapters.

## Candidate topology

Each peer captures local MIDI and voice/audio, publishes streams to other participants (or a relay), receives remote streams, synthesizes incoming MIDI locally, decodes incoming audio, and builds an **individual headphone mix**. Local monitoring must avoid unnecessary network round trips.

Peer-to-peer, relay and centralized mixing are **options to benchmark**, not preselected requirements. Local mDNS discovery does not solve Internet rendezvous or NAT traversal.

## Timing and musical constraints

- Measure one-way and round-trip latency, jitter, drift, packet loss and audio-device buffering separately.
- Use explicit timestamps and a documented clock-mapping strategy for MIDI and audio.
- A jitter buffer improves smoothness at the cost of latency.
- Perfect simultaneous live interaction over arbitrary Internet distances is **not guaranteed**.
- Prioritize honest performance measurements and graceful degradation over claims of zero latency.

## Initial demonstration

Two peers, one running AROS and another running Linux:
1. Both connect a MIDI instrument/controller and a microphone.
2. Each hears the other's MIDI instrument (locally synthesized) and voice.
3. Each independently adjusts remote MIDI and voice levels.
4. The local instrument is monitored without network round-trip.
5. Record and report latency, jitter, dropouts and synchronization behavior.

Then expand to a four-member band: MIDI drums, MIDI bass, MIDI keyboard/guitar, and vocals.

## Documentation

- [Architecture and component boundaries](docs/ARCHITECTURE.md)
- [Phased roadmap and acceptance criteria](docs/ROADMAP.md)
- [Research references and porting audit](docs/REFERENCES.md)
- [Open questions and decision log](docs/DECISIONS.md)

## Related projects

- [AROS MIDIHub](https://github.com/JJDSNT/aros-midihub)
- [AROS](https://github.com/aros-development-team/AROS)

## Status and licensing

Planning/research only. No implementation or portability claim is implied. A project license has **not yet been selected**; third-party code must not be copied before checking its license and compatibility.
