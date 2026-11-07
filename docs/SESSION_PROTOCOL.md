# Session protocol — requirements, not wire specification

## Goals
Represent a distributed band as participants, instrument sources and voice/audio sources; coordinate membership and playback without embedding a replacement MIDI transport.

## Logical objects
- **Room:** identifier, human-readable title, access policy, lifecycle.
- **Participant:** stable session-scoped identity, display name, connection state, permissions.
- **Instrument source:** owner, role, MIDI endpoint/capabilities, transport binding, optional synthesizer/program configuration.
- **Audio source:** owner, microphone/instrument type, codec parameters, stream binding.
- **Mix settings:** listener-owned per-source gain/pan/mute, never shared by default.
- **Recording track:** source identity, media type, timestamp domain and segment metadata.

## Lifecycle
Create -> discover/invite -> authenticate/admit -> announce sources and capabilities -> bind streams -> monitor/play -> change membership -> recover/leave -> clean up.

## Required semantics
- Separate **control plane** (room membership, capabilities, source metadata, authorization) from **media/data planes** (native MIDI transport and audio transport).
- Use native DNS-SD for local service discovery where available; do not equate DNS-SD with authentication.
- Ensure unique participant/source IDs within a room and safe source ownership on reconnect.
- Membership changes must trigger note cleanup, audio stream teardown and route cleanup.
- Local instrument monitoring is never required to round-trip through the network.
- Personal mixes are private to each listener.
- MIDI capability negotiation and downgrade must be explicit; preserve unknown supported UMP types where feasible.
- Audio transport must support loss, jitter and rebuffering without blocking MIDI event handling.
- Record the source's timestamp and a correlation to the session timeline; never assume audio sample clock equals MIDI clock.
- Internet rendezvous, NAT traversal, encryption and relay topology are **pending decisions**, not implicit promises.

## Deferred wire choices
Serialization, room signaling transport, peer authentication, clock synchronization algorithm, codec configuration, and version negotiation must be decided from G0/G1 measurements. Avoid inventing a proprietary network MIDI protocol.

## Instrument fidelity and asset negotiation

A MIDI performance does **not** carry the sound of its source synthesizer. Session setup therefore advertises **instrument render intent** separately from MIDI event transport.

### Proposed instrument manifest (conceptual; not a frozen wire schema)
- Participant/source identity and instrument role
- MIDI protocol/capabilities (MIDI1, UMP, MPE, per-note expression as supported)
- Rendering mode: local soundbank, local plugin/synth, external hardware, audio-only, or hybrid
- Synth engine identifier/version and compatible preset format
- Soundbank identifier, version, cryptographic content hash, size, format, and preset/program/bank selection
- Optional dependency/effects metadata needed for a closer timbral match
- Availability, provenance, sharing permissions and authorized retrieval method
- Fallback policy and fidelity status reported to each listener

### Receiver negotiation
1. Receive a source manifest during admission or instrument changes.
2. Check local synth and soundbank cache by **content hash**, not just filename.
3. If compatible assets exist, bind them to the remote MIDI stream.
4. Otherwise request explicit authorized transfer or retrieval; validate hash, limits, format and provenance before installing in a sandboxed cache.
5. If sharing is prohibited or the renderer is incompatible, offer clearly labeled compatible preset, or **audio from the source instrument**.
6. Acknowledge receiver rendering state: **exact asset / compatible approximation / audio-original / unavailable**. 'Exact asset' does not imply bit-identical audio if synth engine, effects or settings differ.
7. On instrument/preset change, renegotiate without losing note-off and source ownership.

### Safety and ownership
No automatic unrestricted soundbank redistribution; transfers require owner permission and compatible licensing. Authenticate peers, constrain file size, validate content and use a non-executable cache. Asset downloads are control/data operations, **never** on the low-latency MIDI/audio callback path. Local synthesis and instrument asset storage remain native service responsibilities where available; TeleMIDI Band coordinates manifests, consent and user-visible fidelity.
