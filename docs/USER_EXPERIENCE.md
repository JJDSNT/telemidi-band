# User experience — AROS-first

## Core promise
An AROS musician can create or join a band room, connect native MIDI instruments and a microphone, hear other members, and independently adjust the personal monitor mix. No DAW is required for the initial workflow.

## Primary journey
1. Launch TeleMIDI Band on AROS; inspect available MIDI endpoints and audio input/output devices through native services.
2. Create a room or join a discovered/invited room. Show network and transport status.
3. Select instrument role (drums, bass, keys, guitar, voice, other), MIDI input/output and optional microphone.
4. Test local MIDI/audio monitoring without a network round-trip.
5. Connect to the session and hear each remote musician's instrument and voice as independently controllable sources.
6. Adjust personal volume, pan, mute and monitor settings; these changes must not alter other musicians' mixes.
7. Record separately attributed MIDI and audio, leave safely, and release routes/devices.

## Essential screens
- Room browser / create or join
- Device and instrument setup with live meters and capability reporting
- Band room: participants, roles, MIDI/audio health, network diagnostics
- Personal mixer: per-member MIDI and voice controls
- Recording and session diagnostics

## Failure behavior
- Unsupported MIDI 2.0 features must be reported, not silently misrepresented.
- Device unplug, peer loss and reconnection must avoid stuck notes, uncontrolled feedback and stale routes.
- Audio permission/device failure should not prevent MIDI-only participation when feasible.
- Latency indicators must distinguish measured values from estimates.

## MVP acceptance
Two **AROS** computers, each with a MIDI controller and microphone: both hear the other's instrument and voice; both have independent remote-source controls; local monitoring does not traverse the network. Protocol interoperability remains a future test objective, not a requirement to build non-AROS clients.

## Instrument fidelity UX
When a musician joins or changes instruments, show the remote render status per listener: matching bank/renderer, compatible substitute, source audio, or unavailable. Offer opt-in download of legally shareable banks with file size and provenance, and permit choosing a local substitute. Never silently claim the sound is identical. Instrument asset negotiation must not block room admission or local monitoring.
