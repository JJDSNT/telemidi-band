# Telepresence — optional visual participation

**Status (2026-11-19): architectural proposal and feasibility backlog only.** No WebRTC port, video capture, codec or TeleMIDI video session has been demonstrated on AROS. See [roadmap](ROADMAP.md), [native integration contract](INTEGRATION_CONTRACT.md) and [session requirements](SESSION_PROTOCOL.md).

## Goal and scope

Allow band members to see each other while rehearsing or performing over LAN or the Internet. Video is **optional**: MIDI, musical audio, local monitoring, mixing and recording must continue if video is absent, disabled or disconnected. A video delay greater than musical audio delay is acceptable; do not hold back or resynchronize the musical streams to make video appear synchronous.

## Proposed media boundaries

| Plane | Candidate | Ownership |
| --- | --- | --- |
| MIDI performance | Existing native CAMD 43 / MIDIHub transports | Platform MIDI infrastructure |
| Musical audio (voice and instruments) | AHI + candidate AOO with Opus or PCM, subject to G1 feasibility | Native audio and reusable network-audio component |
| Visual telepresence | **Video-only WebRTC** (preferred Internet candidate; not yet selected/ported) | TeleMIDI Band coordination, reusable native video components where justified |
| Session control | Existing room/participant identities, with video capability, consent, stream bindings and state | TeleMIDI Band |

WebRTC video-only means **no microphone track, audio transceiver or SDP audio media section** in the video peer connection. The musical microphone must not be captured a second time for WebRTC. RTP/RTCP are components of WebRTC, not competing alternatives; bare RTP/RTCP + H.264 remains a candidate for simpler native/LAN implementations, with separate security, signaling and NAT requirements.

WebRTC's ICE/STUN/TURN connectivity applies to its own media path; it does **not** automatically make AOO or other musical-audio UDP flows reachable. Assess direct connectivity and relay for musical audio independently.

## Participant and lifecycle model

- A video source belongs to a room participant, but video device IDs and CAMD endpoint IDs are **not** interchangeable.
- Advertise camera availability, user consent, video enabled/muted state, codec and transport capabilities separately from MIDI/audio.
- Join/leave, reconnect, camera switch and temporary video failure must not tear down MIDI or musical audio.
- Never require a camera, browser or WebRTC library to join a music-only session.
- Camera privacy: default off until the participant enables it; provide a clear stop indicator and stop capture/transmission on disable or leave.
- Signaling, authorization, encryption, ICE candidates and TURN credentials need a defined security model; do not expose them through unauthenticated local discovery.
- Video does not enter the musical mix or the audio multitrack recorder. Any future video recording needs its own opt-in, retention and timestamp contract.

## Feasibility and implementation backlog

| ID | Investigation / prototype | Acceptance evidence |
| --- | --- | --- |
| **TP01** | Audit actual AROS camera capture interfaces, VHI availability, USB camera support and image formats on target builds; avoid assuming that VHI or a camera driver exists. | Source-linked capability matrix, reproducible capture test or named missing dependency. |
| **TP02** | Audit native video encode/decode options (H.264, MJPEG and any viable alternatives), frame conversion, rendering/UI APIs and CPU/memory cost on relevant AROS architectures. | Measured local camera-to-display prototype, including failure and resource handling. |
| **TP03** | Compare small open-source WebRTC stacks (including libdatachannel as a candidate), dependencies, licensing, C/C++ toolchain, sockets, threading, timers, ICE, DTLS/SRTP and signaling requirements. | Build/dependency report for AROS; select, reject or defer WebRTC based on evidence, not assumption. |
| **TP04** | Demonstrate a video-only two-peer session with authenticated signaling, encrypted media and camera enable/disable; verify **no WebRTC audio track** is negotiated or captured. | Connection trace, SDP inspection, reproducible build and measured video delay/CPU/bandwidth. |
| **TP05** | Measure simultaneous MIDI + musical audio + video on LAN and Internet. Give musical traffic priority by adaptive video bitrate/resolution/framerate or video suspension when necessary. | Jitter, packet loss, musical-audio latency/underruns and CPU measured with video on/off; music continues through video loss. |
| **TP06** | Evaluate direct WebRTC vs TURN relay and the **independent** NAT/relay requirements of AOO/audio. Consider RTP/RTCP-only LAN fallback if WebRTC is impractical on AROS. | Documented network topologies and clear limitations, including IPv4/IPv6 behavior. |
| **TP07** | Integrate optional participant camera tiles, video capability state and consent into room lifecycle without changing MIDI/recording contracts. | Join/leave/reconnect tests, camera-off and video-failure tests, no regression in music-only sessions. |

## Gate placement

- **G0:** TP01–TP03 inventory and feasibility research may run alongside native integration audit, without blocking MIDI/audio milestones.
- **G2/G3:** optional local two-peer/four-person video experiment **only after** the audio/MIDI baseline is measurable; no new G2 exit requirement.
- **G4:** Internet video-only WebRTC prototype (TP04–TP06), independent of the musical-audio transport's NAT and relay decisions.
- **Later:** UI integration (TP07) when the transport and native capture/codec feasibility are established.

## Engineering decisions and non-decisions

1. Prefer WebRTC video-only for Internet telepresence, **conditional on AROS feasibility**. No dependency is approved or vendored yet.
2. AOO + Opus/PCM remains an **audio candidate**, not a deployed protocol. Do not route the musical microphone through WebRTC by default.
3. No requirement for frame-accurate video-to-MIDI sync. Record approximate video timing for diagnostics if useful, but protect the musical latency budget.
4. Do not add telepresence to CAMD, MIDI-CI or the MIDIHub UMP codec. Extract generic video services into AROS only if a reusable, source-verified need emerges.
5. Do not claim WebRTC solves network QoS for other transports; competing video and audio still share the physical uplink.
