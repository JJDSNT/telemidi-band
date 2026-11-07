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
