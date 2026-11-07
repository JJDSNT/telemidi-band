# Competitive analysis — evidence review (2026-11-07)

## Scope and evidence policy
This document compares **publicly documented** behavior, not measured performance. "Not confirmed" means no verified claim from the reviewed sources; it does **not** mean a competitor lacks the feature. TeleMIDI Band capabilities remain **planned**, not implemented.

## Direct comparisons

| Product | Publicly confirmed | Not established in this review | Implication |
| --- | --- | --- | --- |
| Muse | MIDI input/output streaming between participants, DAW integration, remote audio, recording, talkback; vendor warns Internet latency may prevent synchronized live jams | MIDI 2.0 UMP end-to-end, MIDI-CI, per-note expressivity, local MIDI synthesis strategy | Strongest comparison for hybrid MIDI + audio; do not claim hybrid is unique |
| Play4Me | Browser-based workstation, synthesized instruments, live audio streams, MIDI/audio recording, real-time collaboration | Wire format for remote notes, UMP, MIDI-CI, detailed per-participant monitor controls | Competes on the combined creative workflow, not necessarily system integration |
| Wire Chord | Shared browser jam rooms; MIDI notes sent to room and synthesized at receiving browsers; local instrument monitored on hardware/DAW | MIDI 2.0 UMP, robust audio/voice transport details, per-source mixer, timing guarantees | Direct precedent for MIDI-event transport + local synthesis |
| SonoBus | Network audio collaboration and individual monitoring; open source | MIDI-event-first distributed band semantics | UI/audio reference, not a complete MIDI application |
| Jamulus | Real-time collaborative audio with a mixer; open source | Native end-to-end MIDI event collaboration | Audio architecture reference |

### Sources
- Muse FAQ: https://www.musesessions.co/faq
- Muse plans: https://www.musesessions.co/plans
- Play4Me: https://play4.me/
- Wire Chord: https://wirechord.com/ and https://wirechord.com/faq
- SonoBus: https://github.com/sonosaurus/sonobus
- Jamulus: https://github.com/jamulussoftware/jamulus

## What is *not* a differentiator by itself
- Sending MIDI over the network.
- Combining MIDI with remote voice/audio.
- Playing remote MIDI through a local synthesizer.
- Having a virtual rehearsal room.

All have precedents; the TeleMIDI Band proposition must be tested as an integrated experience.

## Candidate differentiators — hypotheses, not claims
1. **AROS-native systemic integration:** consume CAMD 43, MIDIHub routing, native network MIDI and DNS-SD rather than shipping another MIDI stack.
2. **MIDI 2.0-native session model:** preserve UMP packet semantics and supported per-note controllers; negotiate endpoint capabilities where the AROS implementation actually supports them.
3. **Musician/instrument-aware room:** identify participants, instruments, endpoints and stream types; distinguish MIDI performance from audio microphone feeds.
4. **Independent hybrid monitoring:** receive each remote member's MIDI/audio, synthesize MIDI locally and mix both per listener without network-loop local monitoring.
5. **Inspectable, separately recorded performance data:** capture timestamped MIDI and audio tracks for later editing and alignment.

None is established as exclusive to AROS. Competitive superiority requires hands-on benchmarks and task-based usability comparisons.

## MIDI 2.0 audit questions
- Does the source endpoint expose UMP or only MIDI 1 byte streams?
- Are UMP message types and per-note expression preserved across transport, router, synthesizer and recorder?
- Is MIDI-CI discovery/profile/property exchange available at the right API boundary? Do not assume all CAMD 43 functionality is implemented.
- How are unsupported features downgraded without silent information loss?
- How are note lifecycle and panic/recovery handled after packet loss or peer departure?
- Does the receiving synthesizer actually render per-note expressivity, or merely accept events?

## Synchronization and audio research
AOO provides a C-facing API from a C++17 implementation, peer audio sources/sinks, jitter buffering, clock/drift handling and network diagnostics. This makes it a **candidate** for audio, not a proven AROS port. The upstream warns that its API is alpha and can change.

**Important source distinction:** the SonoBus-associated `essej/aoo` fork explicitly says it is an old version used only to build SonoBus and directs new users to current upstream. Study current AOO at https://git.iem.at/aoo/aoo (GitHub mirror https://github.com/Spacechild1/aoo).

Required AROS checks: compiler/C++17, dependencies and licenses, BSD sockets, threading, monotonic clocks, atomics, audio callback model, AHI full duplex, sample-rate conversion, jitter and CPU cost. AOO clock behavior does not automatically solve MIDI-to-audio timeline correlation.

## Proposed competitive acceptance scenarios
1. **Setup:** from a connected AROS MIDI controller to hearing a remote player's notes; count manual steps and configuration errors.
2. **Hybrid room:** two AROS peers, each with MIDI controller + microphone, both hearing the other's MIDI and voice.
3. **Per-listener mix:** one member changes remote drum/voice levels without altering anyone else's mix.
4. **Expressivity:** transmit a known UMP/per-note controller test vector and verify it at receiving MIDI and synthesis boundaries.
5. **Recovery:** disconnect a peer during held notes; no indefinite stuck notes or audio feedback.
6. **Performance:** measure input-to-audible-output delay, RTT, jitter, drift, packet loss, underruns and CPU under identical conditions.
7. **Studio:** independently export MIDI and audio with timing and participant attribution.

## Positioning
**AROS-first, interoperable by standard protocols, MIDI-native and hybrid by design.** The intended showcase is not that remote jamming is unavailable elsewhere, but that an AROS application can compose native OS MIDI capabilities into a complete collaborative band experience.
